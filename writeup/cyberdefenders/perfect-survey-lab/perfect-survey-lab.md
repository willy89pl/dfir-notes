# Perfect Survey Lab

## Scenario
The Wowza Sport website recently came under a suspected cyberattack. Internal stakeholders believe that a rival competitor may be behind the incident. On the day of the attack, monitoring systems detected a sudden and abnormal surge in incoming web requests, overwhelming the company’s online services. 

Your task is to investigate the compromised web server, which also functions as the domain controller for the Wowza Sport environment. You are expected to analyze logs, identify the source and method of the attack, and assess the potential impact on both the website and the organization’s Active Directory infrastructure

## Tools
Praca na logach w Splunk. Logi a webserwera i AD.

## Q&A

#### Q1
The attacker performed service enumeration on the target web server to identify the content management system. What CMS platform was discovered hosting the website?
<details>
  <summary>Answer: Click me</summary>
  Wordpress
</details>

```
Przechodząc do sourcetype związanego z logami serwera www od razu rzuca nam się nazwa CMS'a.
```

#### Q2
A specialized scanning tool was deployed to enumerate CMS-specific vulnerabilities. What is the name and version of the scanning tool used against the CMS?
<details>
  <summary>Answer: Click me</summary>
  WPScan, 3.8.28
</details>

```
Patrzać na używane useragenty narzędzia same nam się przedstawiają. Możemy tez użyć prostego zapytanie SPL.
```
```sql
index=* sourcetype=access_combined
| stats count by useragent
```

#### Q3
Throughout the attack chain, all malicious traffic originated from a single source IP address used by the threat actor. What is the IP address that the threat actor used for their attack?
<details>
  <summary>Answer: Click me</summary>
  47.128.63.0
</details>

```
Adres związany z skanowaniem wykonywanym przez WPScan.
```

#### Q4
The scanner identified a vulnerable third-party plugin installed on the CMS. What is the name of this vulnerable plugin?
<details>
  <summary>Answer: Click me</summary>
  Perfect Survey
</details>

```
Skupiamy się na zapytaniach z WPScan, odrzucamy nieudane zapytania, na liście uri_path nzjadziemy udane zapytania z ścieżką /plugins/
```

#### Q5
The attacker attempted to exploit the vulnerable plugin for remote code execution. At what exact timestamp did the scanning tool attempt the RCE exploit? (in 24-hour format)
<details>
  <summary>Answer: Click me</summary>
  2025-09-18 02:25
</details>

```
Po wykryciu podatnego pluginu, zmienia się narzędzie do jego exploitacji. Wykorzystany zostaje sqlmap. Przyadje się też OSINT wiedza o podatnościach w perfect-survey. Przeglądając pierwsze zapytania z sqlmapa widzimy kombi zapytanie które próbuje jednocześnie: sqli, xss, rce. CZas tego zapytania to odpowiedź.
```

#### Q6
An automated tool was used to extract password hashes from the CMS database. What database field name did the tool extract containing the password hash?
<details>
  <summary>Answer: Click me</summary>
  user_pass
</details>

```
Przeglądajac dalsze zapytania sqlmap możemy widać Blind SQLi. zapytania wyciągają nazwę użytkownika a następnie hash pliku. Przeglądanie zapytań w Splunk ożemy sobie uczytelnić używając urldecode() PRzykąłd zapytania poniżej.
```
```sql
index=* source="access.log" useragent="sqlmap/1.9.3#stable (https://sqlmap.org)" status!=400 status!=404
| eval decoded = urldecode(uri_query)
| table  _time, status, decoded
| sort  by _time asc
```

#### Q7
After cracking the extracted hash, the attacker gained valid server credentials. Which user's password was compromised for initial authentication?
<details>
  <summary>Answer: Click me</summary>
  mourinho.j
</details>

```
Tutaj przyda się magia Splunk. Analizując jak działa sqlmap i patrząc na zapytania możemy zauważyć coś so się nazywa "blind boolean SQL injection" . Chodzi o to że z serwera możemy wyciągnąć tylko informację binarnie (tak/nie) na podstawie kodu odpowiedzi html w informacji zwrotnej. Czyli w skórcie pytamy w tym przypadku o kolejne litery pierwszego usera w tabeli "user_login". Konsekwetnie pytajac możemy ustalić kolejne litery znajdujące się w nazwie usera. Potem możemy to wykonać dla kolejnych userów, potem możemy odpowiadajce im hashe hasełł wyciągnąć. Zapytania sqlmapa są dośc wyrafinowane, i wczesniej jest pytanie o ilosc znakow, potem wyciaganie w wartosci asci. Do analizy. Poniżej już dopracowane zapytanie SPL które wyciąga nazwę drugiego z kolei usera z logów pozostawionych przez sqlmap.
```
```sql
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

#### Q8
A Kerberoasting attack was conducted after compromising the first user. Which user was targeted by this attack?
<details>
  <summary>Answer: Click me</summary>
  alonso.x
</details>

```
W pytaniu jest wprost wskazane że wykonany był Kerberoasting. Szukamy zatem silnych jego wskazań. Event 4769(A Kerberos service ticket was requested) oraz TicketEncryptionType RC4(0x17) Pierwszy event zawiera nasza odpowiedż pod ServiceName.
```

#### Q9
A new account was created in preparation for a domain privilege escalation. What is the name of this machine account?
<details>
  <summary>Answer: Click me</summary>
  MADRID$
</details>

```
Zapytanie jest o machine account, interesuje nas zatem event 4741, w nim znajdziemy autora z Q8 inazwę 
```

#### Q10
The attacker modified an AD attribute to enable Resource-Based Constrained Delegation. Which specific attribute was modified on the computer account?
<details>
  <summary>Answer: Click me</summary>
  msDS-AllowedToActOnBehalfOfOtherIdentity
</details>

```
Rozgrywa się tutaj scenariusz związany z delegeacją typu Resource-Based Constrained Delegation, czyli konfiguracja komu ufamy jest w specyficznym atrybicue na odpowiednim obiekcie tej usługi w AD.
```

#### Q11
The attacker requested a service ticket through the configured Resource-Based Constrained Delegation chain. Which account was targeted for the service ticket request via RBCD?
<details>
  <summary>Answer: Click me</summary>
  APP-BKUP01$
</details>

```
komentarz, wyjasnienie etc
```

#### Q12
The attacker exploited Active Directory Certificate Services to obtain privileged account hashes. Which high-privilege account was targeted through AD CS escalation?
<details>
  <summary>Answer: Click me</summary>
  Administrator
</details>

```
Pytanie sugeruje dalsza eskalację poprzez Active Directory Certificate Services. Możemy poszukać żądań wystawienia certyfikatu,  4886(Certificate Services received a certificate request) W zawartości znajdziemy żądającego Requester=WORLDCUP\APP-BKUP01$ oraz Attributes gdzie będzie szablon certyfikatu oraz SAN z nasza odpowiedzą.
```

#### Q13
A misconfigured certificate template enabled the AD CS privilege escalation. What is the name of the vulnerable certificate template exploited?
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
Tutaj analogicznie do Q12, w tym samym żądaniu jest informacja o templatce certyfikatu.
```


## tl;dr czyli Kill Chain
1. Reconnaissance
  - skanowanie Wordpressa za pomocą WPScan z adresu 47.128.63.0 [2025-09-18 02:15:00] ~ [2025-09-18 02:20:00]

2. Initial Access
  - Wykrycie podatnego pluginu Perfect Survey [2025-09-18T02:20:16.000+00:00]
  - Exploitacja podatności narzędziem sqlmap od [2025-09-18T02:23:39.000+00:00]

3. Execution
  - wyciągnięcie użytkownika(mourinho.j) oraz hashu jego hasła via (blind boolean SQL injection) [2025-09-18 02:23:39] ~ [2025-09-18 03:11:50]
  - Kerberoasting attack (EventID=4769)(ServiceName=alonso.x)(TicketEncryptionType=0x17) Zdobycie konta alonso.x [2025-09-18T02:40:44.913+00:00]
  
4. Privilege Escalation
  - łańcuch zdarzeń zwiazanych z Resource-Based Constrained Delegation
   - 4741(A computer account was created) — alonso.x dodaje MADRID$ do domeny [2025-09-18T02:50:30.984+00:00]
   - 4742(A computer account was changed) — alonso.x modyfikuje APP-BKUP01$ (wpisuje MADRID$), niestety brak w logach wprost śladu "msds-allowedtoactonbehalfofotheridentity" [2025-09-18T02:50:45.883+00:00]
   - 4769(A Kerberos service ticket(TGS) was requested) — S4U2Self: bilet do MADRID$ [2025-09-18T02:50:54.882+00:00]
   - 4769(A Kerberos service ticket(TGS) was requested) — S4U2Proxy: MADRID$ → APP-BKUP01$ (TransmittedServices=MADRID$) [2025-09-18T02:50:54.911+00:00]
  - 4886  (Certificate Services received a certificate request) -  żądanie certyfikatu przez (Requester=WORLDCUP\APP-BKUP01$) dla (SAN:upn=administrator@worldcup.ball) [2025-09-18T02:54:24.206+00:00]



# Lessonss&Learned

### Blind SQL Injection (Blind SQLi)
 - to rodzaj ataku typu SQL Injection, w którym aplikacja jest podatna na wstrzyknięcie kodu, ale jej odpowiedzi nie zawierają bezpośrednich wyników zapytania ani komunikatów o błędach bazy danych. 

W przeciwieństwie do klasycznego SQLi, gdzie dane (np. hasła) pojawiają się wprost na stronie, w wersji "ślepej" atakujący musi zadawać bazie danych pytania typu tak/nie i obserwować reakcję serwera. 

Główne rodzaje Blind SQLi
Boolean-based (oparty na logice): Atakujący wysyła zapytania sprawdzające prawdziwość warunku. Jeśli strona ładuje się normalnie, warunek jest prawdziwy; jeśli treść się zmienia lub znika – fałszywy.
Time-based (oparty na czasie): Wykorzystuje funkcje wstrzymujące działanie bazy danych (np. SLEEP()). Jeśli serwer odpowiada z opóźnieniem, oznacza to, że wstrzyknięty warunek został spełniony. 

### jak sqlmap pytał w tym labie

```
MID(user_login, N, 1) — wycinanie znaku na pozycji N
ORD(...) — zamiana na kod ASCII
binary search przez porównania > X
200 = TAK, 404 = NIE — jedyna informacja jaką daje serwer
```

### Kerberoasting
 - atak polegający na wyłudzeniu biletów TGS dla kont z ustawionym SPN, a następnie złamaniu ich offline.

#### Jak działa?
Atakujący (zwykły użytkownik domenowy) pyta DC o bilet TGS dla konta usługowego
DC wydaje bilet zaszyfrowany hashem konta usługowego
Atakujący zabiera bilet i łamie go offline (Hashcat, John)
Rezultat: hasło konta usługowego

#### Kluczowe szczegóły
Nie potrzeba uprawnień admina — wystarczy dowolne konto domenowe
Celowo żąda się szyfrowania RC4 (0x17) zamiast AES — łatwiejsze do złamania
Atakowane konta muszą mieć ustawiony SPN

#### Detekcja (Event ID)
EventCo rejestruje4769Żądanie TGS — główny wskaźnik
Podejrzane sygnały: dużo żądań 4769 z jednego IP + Ticket_Encryption_Type=0x17

#### Resource-Based Constrained Delegation (RBCD)
 - To technika eskalacji uprawnień / lateral movement w AD.

W Kerberos delegacja pozwala usłudze działać "w imieniu użytkownika" — czyli przesyłać jego tożsamość dalej do innych usług.
RBCD to nowszy wariant, gdzie zamiast konfigurować delegację na koncie usługowym, konfiguruje się ją na obiekcie docelowym — atrybut msDS-AllowedToActOnBehalfOfOtherIdentity.

Trzy typy delegacji w AD:
|Typ|Opis|
|Unconstrained|Konto może impersonować użytkownika wobec dowolnej usługi|
|Constrained (KCD)|Konto może impersonować tylko wobec określonych usług|
|Resource-Based (RBCD)|Konfiguracja jest po stronie zasobu docelowego, nie konta usługowego|

#### RBCD 
 - Atak eskalacji uprawnień w AD wykorzystujący mechanizm Resource-Based Constrained Delegation — czyli możliwość impersonowania dowolnego użytkownika (w tym Administratora) wobec docelowej usługi.

Wymagania:
Dowolne konto domenowe
Uprawnienie GenericWrite / WriteProperty na obiekcie docelowego komputera w AD
ms-DS-MachineAccountQuota > 0 (domyślnie = 10)

Łańcuch ataku

Zdobyć konto domenowe (własne lub przejęte)
Sprawdzić uprawnienia — czy konto ma GenericWrite na docelowym obiekcie (np. BloodHound)
Dodać fałszywy komputer do domeny — EvilComputer$ (wykorzystując MachineAccountQuota)
Zmodyfikować atrybut msDS-AllowedToActOnBehalfOfOtherIdentity na docelowym komputerze — wpisać EvilComputer$
S4U2Self — EvilComputer$ prosi DC o bilet jakby Administrator pytał o EvilComputer$
S4U2Proxy — EvilComputer$ prosi DC o bilet do docelowej usługi jako Administrator
Użyć biletu → dostęp do usługi jako Administrator

Wizualnie
EvilComputer$ → DC: S4U2Self  → bilet "Administrator → EvilComputer$"
EvilComputer$ → DC: S4U2Proxy → bilet "Administrator → TargetPC"
EvilComputer$ → TargetPC:       dostęp jako Administrator!
Narzędzia
EtapNarzędzieRekonesans uprawnieńBloodHound / PowerViewDodanie komputeraImpacket addcomputer.py / PowerMadModyfikacja atrybutuPowerView (Set-ADComputer)S4U2Self + S4U2ProxyImpacket getST.py / RubeusUżycie biletuPass-the-Ticket → psexec, wmiexec
Kluczowa różnica vs KCD

KCD — delegacja konfigurowana na koncie usługowym (wymaga admina domeny)
RBCD — delegacja konfigurowana na obiekcie docelowym (wystarczy GenericWrite!)

# AD CS (Active Directory Certificate Services) — Notatki

## Podstawowe pojęcia

- **Certificate Authority (CA)** — urząd certyfikacji, serwer wystawiający certyfikaty w domenie
- **Certificate Template** — szablon certyfikatu, definiuje kto może prosić o certyfikat i do czego służy
- **Certificate Signing Request (CSR)** — prośba o wystawienie certyfikatu składana do CA
- **Extended Key Usage (EKU)** — określa do czego certyfikat może być użyty (np. logowanie, szyfrowanie)
- **Subject Alternative Name (SAN)** — alternatywna tożsamość wpisana w certyfikacie
- **PKINIT (Public Key Initial Authentication)** — mechanizm Kerberosa pozwalający uwierzytelnić się certyfikatem zamiast hasłem

---

## Jak działa uwierzytelnianie certyfikatem w AD

1. Użytkownik składa **CSR** do CA na podstawie szablonu
2. CA wystawia certyfikat (plik `.pfx` — certyfikat + klucz prywatny)
3. Użytkownik prezentuje certyfikat DC przez mechanizm **PKINIT**
4. DC zwraca **TGT** + **NT hash** konta (mechanizm **UnPAC-the-Hash**)
5. Atakujący może używać TGT (Pass-the-Ticket) lub NT hash (Pass-the-Hash)

---

## ESC1 — podatna konfiguracja szablonu

### Warunki podatności

| Warunek | Opis |
|---------|------|
| **Enrollee Supplies Subject** | Wnioskodawca sam wpisuje wartość SAN |
| **Client Authentication w EKU** | Certyfikat służy do logowania do domeny |
| **Niskie uprawnienia do enrollment** | Zwykły użytkownik domenowy może poprosić o certyfikat |

### Dlaczego to jest niebezpieczne
Atakujący składa CSR na podatnym szablonie wpisując w polu **SAN** tożsamość Administratora (`upn=administrator@domena.local`) zamiast swojej. CA wystawia certyfikat bez weryfikacji czy wnioskodawca faktycznie jest Administratorem.

---

## Łańcuch ataku ESC1

1. Rekonesans — znalezienie podatnego szablonu
2. Złożenie CSR z SAN = Administrator
3. Otrzymanie certyfikatu `.pfx`
4. Wymiana certyfikatu na TGT + NT hash przez PKINIT
5. Dostęp do domeny jako Administrator

---

## Narzędzia

| Etap | Narzędzie |
|------|-----------|
| Rekonesans szablonów | **Certipy** / **Certify** / **BloodHound** |
| Złożenie CSR (ESC1) | **Certipy req** |
| Wymiana certyfikatu na TGT | **Certipy auth** |
| Użycie NT hash | **Pass-the-Hash** (psexec, wmiexec) |

---

## Detekcja 

### Kluczowe Event IDs

| Event ID | Źródło | Co rejestruje |
|----------|--------|---------------|
| **4886** | CA | Żądanie certyfikatu (CSR) |
| **4887** | CA | Certyfikat wystawiony |
| **4888** | CA | Żądanie odrzucone |
| **4768** | DC | TGT request — widać użycie PKINIT |

### Główny wskaźnik ataku — 4887
Szukaj sytuacji gdzie **wnioskodawca (requester)** różni się od **tożsamości w SAN (subject)**:

W Splunk na przykład:
```spl
index=wineventlog EventCode=4887
| rex field=Message "Certificate Template: (?<template>[^\r\n]+)"
| rex field=Message "Subject: (?<subject>[^\r\n]+)"
| rex field=Message "Requester: (?<requester>[^\r\n]+)"
| where requester != subject
| table _time, requester, subject, template
```

### Szerszy łańcuch detekcji
```spl
index=wineventlog EventCode IN (4886, 4887, 4768)
| stats count by EventCode, Account_Name, _time
| sort _time
```

### Co jest podejrzane
- Zwykły użytkownik domenowy składa CSR na szablonie z **Client Authentication** w EKU
- Pole **SAN** zawiera inne konto niż wnioskodawca (np. `upn=administrator@domena.local`)
- Zaraz po 4887 pojawia się 4768 z tego samego konta — wymiana certyfikatu na TGT

---

## Red flags w szablonach (do sprawdzenia w środowisku)

- `CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT` — wnioskodawca kontroluje SAN
- **EKU:** `Client Authentication` / `Smart Card Logon` / `Any Purpose`
- **Enrollment rights** dla `Domain Users` lub `Authenticated Users`