# Tytuł labka
Utilize OSINT, VirusTotal, and crt.sh to analyze a multi-stage malvertising campaign, identifying initial access, malware payloads, and attacker infrastructure.

## Scenario
In March 2024, the security team at a mid-sized investment advisory firm noticed a wave of support tickets from employees reporting system slowdowns and suspicious pop-ups after searching for financial recovery tools online. Internal traffic logs showed multiple connections to an unfamiliar domain, treasurybanks.org, shortly before endpoints began exhibiting abnormal behavior. Preliminary threat intelligence suggests this domain is part of a broader infrastructure serving malicious content under the guise of legitimate financial assistance.

Your task is to investigate artifacts from one of the compromised endpoints to uncover the attack chain. Determine how the initial access was gained, what was downloaded, and how attacker infrastructure—including certificates, domains, and cloud providers—played a role in the campaign. 

## Tools
Publicznie dostępne narzędzia takie jak VirusTotal czy online sandboxy, a także różnego rodzaju bazy wiedzy typu Malpedia, AlienVault etc.

## Q&A

#### Q1
What type of malicious advertising method was used to initially lure victims to the fake financial websites?
<details>
  <summary>Answer: Click me</summary>
  Malvertising
</details>

```
Samo odnalezienie strony na dzień dzisiejszy jest trudne, na archive.org odnalazłem jakąś kopię. ALe to raczej nie to. Na UrlScan jest troche informacji i na tym będzie trzeba bazować. Screeshot strony przedstawia informację o *nieodebranych pieniądzach* ,wygląda na próbę oszustwa. Ogólna zbiorcza nazwa dla złośliwych treści reklamowych które prowadzą następnie do wyłudzania, piekierowań lub innych złośliwych treści do pobrania to *nazwa udzielona jako odpowiedź*
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