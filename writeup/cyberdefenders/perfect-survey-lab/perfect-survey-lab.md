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
1. Initial Access & Reconnaissance
  - asdf

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