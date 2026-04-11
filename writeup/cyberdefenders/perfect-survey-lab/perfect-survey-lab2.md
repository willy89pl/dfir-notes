---
tags:
  - cyberdefenders
  - wordpress
  - active-directory
  - web-attack
  - kerberos
  - adcs
  - threat-hunting
platform: cyberdefenders
category: threat-hunting
difficulty: medium
date: 2026-04-11
---

# Perfect Survey Lab

> Analiza ataku na serwer webowy z WordPressem, który pełnił jednocześnie rolę kontrolera domeny. Łańcuch ataku prowadzi od SQL Injection przez Kerberoasting i RBCD do eskalacji przez AD CS.

## Scenario
The Wowza Sport website recently came under a suspected cyberattack. Internal stakeholders believe that a rival competitor may be behind the incident. On the day of the attack, monitoring systems detected a sudden and abnormal surge in incoming web requests, overwhelming the company's online services.

Your task is to investigate the compromised web server, which also functions as the domain controller for the Wowza Sport environment. You are expected to analyze logs, identify the source and method of the attack, and assess the potential impact on both the website and the organization's Active Directory infrastructure.

---

## Tools
| Narzędzie | Do czego użyte |
|-----------|----------------|
| Splunk | analiza logów webservera i AD |

---

## Q&A

### Q1
The attacker performed service enumeration on the target web server to identify the content management system. What CMS platform was discovered hosting the website?

<details>
  <summary>Answer: Click me</summary>
  Wordpress
</details>

```
Przechodząc do sourcetype związanego z logami serwera www od razu rzuca nam się nazwa CMS'a.
```

---

### Q2
A specialized scanning tool was deployed to enumerate CMS-specific vulnerabilities. What is the name and version of the scanning tool used against the CMS?

<details>
  <summary>Answer: Click me</summary>
  WPScan, 3.8.28
</details>

```
Patrząc na używane useragenty narzędzia same nam się przedstawiają.
```
```spl
index=* sourcetype=access_combined
| stats count by useragent
```

---

### Q3
Throughout the attack chain, all malicious traffic originated from a single source IP address used by the threat actor. What is the IP address that the threat actor used for their attack?

<details>
  <summary>Answer: Click me</summary>
  47.128.63.0
</details>

```
Adres związany ze skanowaniem wykonywanym przez WPScan.
```

---

### Q4
The scanner identified a vulnerable third-party plugin installed on the CMS. What is the name of this vulnerable plugin?

<details>
  <summary>Answer: Click me</summary>
  Perfect Survey
</details>

```
Skupiamy się na zapytaniach z WPScan, odrzucamy nieudane zapytania — na liście uri_path znajdziemy udane zapytania ze ścieżką /plugins/.
```

---

### Q5
The attacker attempted to exploit the vulnerable plugin for remote code execution. At what exact timestamp did the scanning tool attempt the RCE exploit? (in 24-hour format)

<details>
  <summary>Answer: Click me</summary>
  2025-09-18 02:25
</details>

```
Po wykryciu podatnego pluginu zmienia się narzędzie — wykorzystany zostaje sqlmap.
Przydaje się też OSINT wiedza o podatnościach w perfect-survey.
Przeglądając pierwsze zapytania z sqlmapa widzimy combo zapytanie próbujące jednocześnie: SQLi, XSS, RCE.
Czas tego zapytania to odpowiedź.
```

---

### Q6
An automated tool was used to extract password hashes from the CMS database. What database field name did the tool extract containing the password hash?

<details>
  <summary>Answer: Click me</summary>
  user_pass
</details>

```
Przeglądając dalsze zapytania sqlmap widać Blind SQLi — zapytania wyciągają nazwę użytkownika, a następnie hash hasła.
Przeglądanie zapytań w Splunk możemy sobie uczytelniać używając urldecode().
```
```spl
index=* source="access.log" useragent="sqlmap/1.9.3#stable (https://sqlmap.org)" status!=400 status!=404
| eval decoded = urldecode(uri_query)
| table _time, status, decoded
| sort by _time asc
```

---

### Q7
After cracking the extracted hash, the attacker gained valid server credentials. Which user's password was compromised for initial authentication?

<details>
  <summary>Answer: Click me</summary>
  mourinho.j
</details>

```
Tutaj przydaje się magia Splunk. sqlmap używa Blind Boolean SQL Injection — [[blind-sqli]].
Serwer odpowiada tylko binarnie (200=TAK / 404=NIE), a sqlmap odgaduje kolejne znaki hashu przez binary search na kodach ASCII.

Poniżej zapytanie SPL wyciągające nazwę drugiego użytkownika z logów sqlmapa:
```
```spl
index=* source="access.log" useragent="sqlmap/1.9.3#stable (https://sqlmap.org)" *user_login*
| eval decoded = urldecode(uri_query)
| rex field=decoded ".*CAST\(user_login.*LIMIT (?<user_idx>\d+),1\),(?<char_pos>\d+),1\)\)>(?<value>\d+)"
| where user_idx=1 AND status=200
| stats max(value) as final_value by char_pos
| eval final_value = final_value+1
| eval char = printf("%c", final_value)
| table char_pos, final_value, char
| sort by char_pos
```

---

### Q8
A Kerberoasting attack was conducted after compromising the first user. Which user was targeted by this attack?

<details>
  <summary>Answer: Click me</summary>
  alonso.x
</details>

```
Szukamy silnych wskazań Kerberoastingu — [[kerberoasting]].
EventID=4769 + TicketEncryptionType=RC4(0x17). Pierwszy taki event zawiera odpowiedź pod ServiceName.
```

---

### Q9
A new account was created in preparation for a domain privilege escalation. What is the name of this machine account?

<details>
  <summary>Answer: Click me</summary>
  MADRID$
</details>

```
Pytanie dotyczy machine account — interesuje nas EventID=4741.
W nim znajdziemy autora (alonso.x z Q8) i nazwę nowego konta.
```

---

### Q10
The attacker modified an AD attribute to enable Resource-Based Constrained Delegation. Which specific attribute was modified on the computer account?

<details>
  <summary>Answer: Click me</summary>
  msDS-AllowedToActOnBehalfOfOtherIdentity
</details>

```
Scenariusz RBCD — [[rbcd]].
Konfiguracja "komu ufamy" jest zapisana w atrybucie msDS-AllowedToActOnBehalfOfOtherIdentity
na obiekcie docelowym w AD.
```

---

### Q11
The attacker requested a service ticket through the configured Resource-Based Constrained Delegation chain. Which account was targeted for the service ticket request via RBCD?

<details>
  <summary>Answer: Click me</summary>
  APP-BKUP01$
</details>

```
Śladem takiej operacji jest EventID=4742 (A computer account was changed),
gdzie przejęty alonso.x modyfikuje atrybut z Q10 dla konta będącego odpowiedzią.
```

---

### Q12
The attacker exploited Active Directory Certificate Services to obtain privileged account hashes. Which high-privilege account was targeted through AD CS escalation?

<details>
  <summary>Answer: Click me</summary>
  Administrator
</details>

```
Dalsza eskalacja przez AD CS — [[adcs-esc1]].
EventID=4886 (Certificate Services received a certificate request).
W zawartości: Requester=WORLDCUP\APP-BKUP01$, SAN z odpowiedzią.
```

---

### Q13
A misconfigured certificate template enabled the AD CS privilege escalation. What is the name of the vulnerable certificate template exploited?

<details>
  <summary>Answer: Click me</summary>
  WorkstationAuth_Internal
</details>

```
Analogicznie do Q12 — w tym samym żądaniu (EventID=4886) jest informacja o szablonie certyfikatu.
```

---

## Timeline

### 1. Reconnaissance
| Czas (UTC) | Zdarzenie |
|------------|-----------|
| 2025-09-18 02:15:00 ~ 02:20:00 | Skanowanie WordPressa przez WPScan z IP 47.128.63.0 |

### 2. Initial Access
| Czas (UTC) | Zdarzenie |
|------------|-----------|
| 2025-09-18T02:20:16.000+00:00 | Wykrycie podatnego pluginu Perfect Survey |
| 2025-09-18T02:23:39.000+00:00 | Rozpoczęcie exploitacji pluginu przez sqlmap |

### 3. Credential Access
| Czas (UTC)                     | Zdarzenie                                                                |
| ------------------------------ | ------------------------------------------------------------------------ |
| 2025-09-18 02:23:39 ~ 03:11:50 | Blind Boolean SQLi — wyciągnięcie użytkownika `mourinho.j` i hasha hasła |
| 2025-09-18T02:40:44.913+00:00  | Kerberoasting — EventID=4769, ServiceName=alonso.x, EncType=0x17         |

### 4. Privilege Escalation
| Czas (UTC) | Zdarzenie |
|------------|-----------|
| 2025-09-18T02:50:30.984+00:00 | EventID=4741 — alonso.x dodaje MADRID$ do domeny |
| 2025-09-18T02:50:45.883+00:00 | EventID=4742 — alonso.x modyfikuje APP-BKUP01$ (wpisuje MADRID$ do msDS-AllowedToActOnBehalfOfOtherIdentity) |
| 2025-09-18T02:50:54.882+00:00 | EventID=4769 — S4U2Self: bilet do MADRID$ |
| 2025-09-18T02:50:54.911+00:00 | EventID=4769 — S4U2Proxy: MADRID$ → APP-BKUP01$ (TransmittedServices=MADRID$) |
| 2025-09-18T02:54:24.206+00:00 | EventID=4886 — żądanie certyfikatu przez APP-BKUP01$ dla SAN:upn=administrator@worldcup.ball |
