# MBuchus Lab
Utilize OSINT, VirusTotal, and crt.sh to analyze a multi-stage malvertising campaign, identifying initial access, malware payloads, and attacker infrastructure.

## Scenario
In March 2024, the security team at a mid-sized investment advisory firm noticed a wave of support tickets from employees reporting system slowdowns and suspicious pop-ups after searching for financial recovery tools online. Internal traffic logs showed multiple connections to an unfamiliar domain, treasurybanks.org, shortly before endpoints began exhibiting abnormal behavior. Preliminary threat intelligence suggests this domain is part of a broader infrastructure serving malicious content under the guise of legitimate financial assistance.

Your task is to investigate artifacts from one of the compromised endpoints to uncover the attack chain. Determine how the initial access was gained, what was downloaded, and how attacker infrastructure—including certificates, domains, and cloud providers—played a role in the campaign. 

## Tools
Publicznie dostępne narzędzia takie jak VirusTotal czy online sandboxy, a także różnego rodzaju bazy wiedzy typu Malpedia, AlienVault etc.
---
## Q&A

#### Q1
What type of malicious advertising method was used to initially lure victims to the fake financial websites?
<details>
  <summary>Answer: Click me</summary>
  Malvertising
</details>

```
Samo odnalezienie strony na dzień dzisiejszy jest trudne, na archive.org odnalazłem jakąś kopię. ALe to raczej nie to. Na UrlScan jest troche informacji. Screeshot strony przedstawia informację o *nieodebranych pieniądzach* ,wygląda na próbę oszustwa. Ogólna zbiorcza nazwa dla złośliwych treści reklamowych które prowadzą następnie do wyłudzania, piekierowań lub innych złośliwych treści do pobrania to *nazwa udzielona jako odpowiedź*
```

#### Q2
What filename was downloaded by users who visited the fraudulent fund recovery site?
<details>
  <summary>Answer: Click me</summary>
  q-report-53394.zip
</details>

```
Bez dostępu do oryginalnej strony dostęp do takiego pliku i ewentualna analiza jest nie mozliwa bezpośrednio. Tutaj należy technikami OSINT wyszukac istniejące artykuły, analizy etc. związane z przedmiotową domeną i zagrożeniami z nią związanymi. Idąc tym tropem znajdziemy kilka interesujących stron a przede wszystkim: *https://github.com/PaloAltoNetworks/Unit42-timely-threat-intel/blob/main/2024-03-26-IOCs-for-Matanbuchus-infection-with-Danabot.txt* W środku znjadziemy odpowiedzi do pytań Q2-Q4
```

#### Q3
Which malware family was identified inside the ZIP archive downloaded by users?
<details>
  <summary>Answer: Click me</summary>
  Matanbuchus
</details>

```
Analogicznie do Q2, odpowiedź wynika z analizy z artukułu.
```

#### Q4
What is the primary function of the malware delivered in this campaign?
<details>
  <summary>Answer: Click me</summary>
  Loader
</details>

```
Szybkie wyszukanie onformacji i dowiemy się że malware używany jest jako loader dla kolejnych stage'y.
```

#### Q5
What is the SHA-256 fingerprint of the TLS certificate used by the treasurybanks.org domain and its subdomains?
<details>
  <summary>Answer: Click me</summary>
  329EC925F80DBD831C09B436CBF1B2200119BC43F95E84286F54F8A40AEF73C5	
</details>

```
Sczegóły o certyfikacie możemy pozyskać na poratlu crt.sh
```

#### Q6
What legitimate command-line tool filename was copied to the Temp directory before the JavaScript payload executed?
<details>
  <summary>Answer: Click me</summary>
  curl.exe
</details>

```
Tutaj znów należy wrócić do artykułu z Q2, natrafimy na tool który jest *odpoiwedzią* Dla jasności możemy też na podstawie hashy poszukać analiz w sandboxach online. Widać kolejność wykonywania się procesów, widac też narzędzie będące *odpowiedzią*
```

#### Q7
Which Certificate Authority issued the valid SSL certificates used across the campaign domains?
<details>
  <summary>Answer: Click me</summary>
  GeoTrust
</details>

```
Wiedząc jakiej domeny dotyczy pytanie w serwisie takim jak crt.sh możemy sprawdzić wystawce certyfikatu.
```

#### Q8
When was the treasurybanks.org domain registered according to the current WHOIS information displayed on VirusTotal?
<details>
  <summary>Answer: Click me</summary>
  2023-07-18
</details>

```
Pytanie jest wprost o datę rejestracji. Odczytać ją należy z VT, sa tam do znalezienia informację w sekcji *Whois Lookup*
```

#### Q9
Which cloud infrastructure provider hosted the IP that served the treasurybanks.org site?
<details>
  <summary>Answer: Click me</summary>
  alibaba cloud
</details>

```
Wymieniajac domenę na ip oczywiście uzyskamy adres i powiazany z nim AS a to jest z kolei przyporządkowane do konkretnej infrastruktury i jej właściciela. Problemem jest tutaj format odpowiedzi. Bo nazwe można spotkać w różnych formach i z różnymi koncówkami. Tricky.
```

#### Q10
What autonomous system (ASN) was associated with an earlier IP address tied to treasurybanks.org Before it changed to another infrastructure?
<details>
  <summary>Answer: Click me</summary>
  AS22612
</details>

```
Tutaj nalezy skorzystać z narzędzi do wyszukania historycznego ip związanego z domeną (np. viewdns.info) a następnie trzeba ten znaleziony ip powiązać z infrastrukturą (np. ipinfo.io)
```

#### Q11
One attacker-controlled domain had a name that hinted at something astro-related and resolved to a network device login panel, unlike the campaign’s other phishing domains. Based on historical DNS records, who was the first recorded IP address owner associated with this domain in 2011?
<details>
  <summary>Answer: Click me</summary>
  ND-CA-ASN
</details>

```
No to było ciekawe. Ogólnie chodziłoby o pivotowanie się po różnych dostepnych śladach aby odkryć pozostałą infrastrukturę. Posiadamy jedynie (wydawałoby się) tylko domenę. Ale to bardzo dużo jak się okazauje. W tym wypadku nawet nie musimy wykonywać profesjonalnej analizy śladów bo juz to zostało wykonane w pewnym artykule na który natrafimy szybko szukając informacji o przedmiotowej domenie. *https://www.embeeresearch.io/tls-certificates-for-threat-intel-dns/*
```

#### Q12
What registrar service did the attacker use to register the treasurybanks.org domain?
<details>
  <summary>Answer: Click me</summary>
  NameCheap
</details>

```
Wertując informację o domenie na VT natrafimy na sekcję *Historical Whois Lookups* w której mamy rejestratora domeny.
```
---
## LessonLEarned:

- Malvertising *rodzaj złośliwej metody reklamowej, odnosi się do sposobu, w jaki atakujący wykorzystują reklamy do infekowania użytkowników*
1. Fake ads / podszywanie się
Reklamy udające legalne rzeczy:
aktualizacje (np. „Update Google Chrome now!”)
instalatory (VPN, antywirus)
strony logowania
Cel: phishing lub instalacja malware

2. Drive-by download
użytkownik nie musi nic klikać
samo wejście na stronę z reklamą uruchamia exploit
często wykorzystuje luki w przeglądarce
Bardziej zaawansowane, dziś rzadsze (ale nadal spotykane)

3. Malicious redirect (przekierowania)
kliknięcie reklamy → przekierowanie przez kilka domen
kończy się na:
phishingu
scamie
malware
często używa:
cloakingu (inna treść dla analityków niż dla ofiary)

4. Scam ads
Reklamy nastawione na oszustwo:
„Wygrałeś iPhone”
fałszywe inwestycje
„Twój komputer jest zainfekowany”
często powiązane z fraudem finansowym

5. Clickjacking / overlay ads
reklama nakłada niewidzialne elementy
użytkownik klika „coś”, a faktycznie:
daje zgodę
instaluje rozszerzenie
subskrybuje usługę

6. Malicious browser extensions via ads
reklama zachęca do instalacji rozszerzenia
rozszerzenie:
kradnie dane
wstrzykuje reklamy
monitoruje ruch

7. SEO poisoning + ads
reklamy prowadzą do stron:
wysoko pozycjonowanych (SEO)
wyglądających legitnie
często: cracki, sterowniki, PDF tools

- Pivotowanie (pivoting) *technika polegająca na przechodzeniu od jednego artefaktu (np. domeny, IP, hasha) do powiązanych elementów, aby odkryć szerszą infrastrukturę atakującego lub zakres incydentu.*

1. Pivot po IP (infrastrukturze)

Sprawdź, na jaki adres IP wskazuje domena.
potem:
jakie inne domeny wskazują na ten sam IP?
narzędzia:
VirusTotal
SecurityTrails
Shodan
szukasz:
współdzielonego hostingu malware
fast-flux

2. Pivot po certyfikacie SSL

Atakujący często używają tego samego certyfikatu.
sprawdź:
SHA1 / fingerprint certyfikatu
CN / SAN (nazwy domen)
narzędzia:
crt.sh
Censys
bardzo skuteczne przy phishingu

3. Pivot po WHOIS (jeśli nie ukryty)

Czasem trafisz na:
ten sam email rejestranta
tę samą organizację
podobne dane
wtedy możesz znaleźć inne domeny
dziś rzadziej (privacy protection)

4. Pivot po wzorcu nazwy domeny

To mega ważne w kampaniach:
Przykład:
secure-paypal-login[.]com
paypal-verification[.]net
paypal-secure-check[.]org
szukasz:
podobnych słów kluczowych
tych samych schematów
techniki:
fuzzy matching
regex
ręczna analiza

5. Pivot po URL / ścieżkach

Jeśli masz URL typu:
/login.php
/verify/account
/update-user
sprawdź:
czy inne domeny mają identyczne ścieżki

6. Pivot po hashach plików

Jeśli domena hostowała malware:
pobierz próbkę
sprawdź hash w VirusTotal
znajdziesz:
inne URL-e
inne domeny hostujące ten sam plik

7. Passive DNS

Historia powiązań domen ↔ IP
możesz zobaczyć:
gdzie domena była wcześniej hostowana
jakie inne domeny były na tym samym IP
narzędzia:
RiskIQ / PassiveTotal
SecurityTrails

8. Pivot po infrastrukturze webowej

Sprawdź:
favicon hash
tytuł strony
fingerprint (np. Shodan)
często identyczne panele phishingowe

9. Pivot po kampanii (OSINT)
Szukaj w:
raportach
Twitter / X
blogach security
często ktoś już znalazł część infrastruktury

10. URL scanning

Wrzuć domenę do:
urlscan.io
VirusTotal
zobacz:
redirect chain
powiązane domeny