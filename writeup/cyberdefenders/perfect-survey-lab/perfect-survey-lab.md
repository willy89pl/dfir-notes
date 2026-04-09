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

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```


## tl;dr czyli Kill Chain
1. Reconnaissance
  - skonowanie Wordpressa za pomocą WPScan z adresu 47.128.63.0 [2025-09-18 02:15:00] ~ [2025-09-18 02:20:00]

2. Initial Access
  - Wykrycie podatnego pluginu Perfect Survey [2025-09-18T02:20:16.000+00:00]
  - Exploitacja podatności narzędziem sqlmap od [2025-09-18T02:23:39.000+00:00]

2. Execution
  - asdf

3. Discovery/Enumeration
  - asdf

4. Credential Access 
  - asdf

5. Lateral Movement
  - asdf

6. Privilege Escalation
  - asdf

7. Container Escape
  - asdf

8. Command & Control (C2)
  - asdf

9. Persistence & Impact
  - asdf

10. Defense Evasion
  - asdf