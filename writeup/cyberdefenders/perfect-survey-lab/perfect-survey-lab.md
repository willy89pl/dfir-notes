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
  - skanowanie Wordpressa za pomocą WPScan z adresu 47.128.63.0 [2025-09-18 02:15:00] ~ [2025-09-18 02:20:00]

2. Initial Access
  - Wykrycie podatnego pluginu Perfect Survey [2025-09-18T02:20:16.000+00:00]
  - Exploitacja podatności narzędziem sqlmap od [2025-09-18T02:23:39.000+00:00]

3. Execution
  - wyciągnięcie użytkownika oraz hashu jego hasła [2025-09-18 02:23:39] ~ [2025-09-18 03:11:50]

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