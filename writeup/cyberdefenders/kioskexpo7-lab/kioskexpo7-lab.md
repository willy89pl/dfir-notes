---
tags:
  - cyberdefenders
  - kioskmode
  - kiosk
platform: cyberdefenders
category: endpoint-forensic
difficulty: medium
date: 2026-04-12
---

# # KioskExpo7 Lab

## Scenario
On October 18, 2025, Wowza Enterprise hosted their first-ever cybersecurity conference. To streamline registration, the IT team configured several laptops in Windows Kiosk mode to display a QR-code registration page for attendee self-service.

After the event concluded, the security team detected suspicious outbound connections originating from one of the kiosk devices KioskExpo7. Surveillance footage revealed a suspicious individual spending an unusually long time interacting with that particular terminal.

Welcome to the KioskExpo7 lab! Step into the role of a forensic investigator and dissect a physical access attack that escalated from a kiosk breakout to full system compromise. The threat actor escaped browser restrictions, escalated privileges, established persistence, and potentially impacted conference attendees.

You have been provided with a KAPE triage image from the compromised kiosk. Your mission is to reconstruct the complete attack timeline, identify the breakout and escalation techniques used, and determine the full scope of the compromise.

---

## Tools
| Narzędzie                 | Do czego użyte                                      |
| ------------------------- | --------------------------------------------------- |
| [[db-browser-for-sqlite]] | czytanie historii przeglądarki                      |
| [[registry-explorer]]     | przeglądanie hive rejestrów                         |
| [[timeline-explorer]]     | przeglądanie csv sprsowanych przez toole Zimmermana |
| [[pecmd]]                 | parser plików prefetch                              |
|                           |                                                     |

---

## Q&A

### Q1
One of the most well-known kiosk breakout techniques involves abusing browser shortcuts (such as Ctrl+O, Ctrl+S, or Ctrl+P) to invoke File Explorer, then clicking the Help button to spawn an unrestricted browser instance. Determining the URL that triggered this escape is crucial for understanding the breakout vector. What is the full URL that was invoked when the threat actor clicked the Help button, allowing them to open a new browser instance outside kiosk restrictions?

<details>
  <summary>Answer: Click me</summary>
  https://go.microsoft.com/fwlink/?LinkID=2004230
</details>

>Odpowiedzi będziemy szukać zatem w historii przeglądarki. Co ważne istotne jest aby szukać we właściwym userze. Plik historii dla Edge leży w : `C:\Users\<user>\AppData\Local\Microsoft\Edge\User Data\Default\History` Przeglądać to możemy np. [[db-browser-for-sqlite]]

---

### Q2
Windows Assigned Access restricts kiosk users to running a single application. Understanding which executable was configured for the kiosk is essential to identify how the threat actor was initially constrained before the breakout. What is the name of the executable configured to launch at kiosk start?

<details>
  <summary>Answer: Click me</summary>
  msedge.exe
</details>

>Odpowiedzi na to pytanie bedziemy szukać w kluczach rejestru. A dokładnie w Hive *Software*, musimy odgrzebać gałąź *AssignedAccessConfiguration* W środku znajdziemy ścieżkę do aplikacją.

---
### Q3
Kiosk applications often use specific command-line arguments to enforce restrictions such as fullscreen mode, disabled navigation, and idle timeouts. Identifying these arguments helps understand what security boundaries the threat actor had to circumvent. What are the full command-line arguments used with the kiosk application?

<details>
  <summary>Answer: Click me</summary>
  --no-first-run --kiosk file:///C:/Users/kiosk/Desktop/index.html --kiosk-idle-timeout-minutes=1440 --edge-kiosk-type=fullscreen
</details>

>Analogicznie do Q2 w miejscu z konfiguracją znajdziemy listę argumentów z którymi powinna uruchamiać się aplikacja.

---
### Q4
After escaping the kiosk restrictions by launching a new browser instance, the threat actor needed to execute commands on the system. However, the Assigned Access policy restricted which executables the kiosk user could run. Identifying what file the threat actor downloaded reveals how they planned to bypass this restriction. What is the name of the file downloaded by the threat actor after escaping the kiosk?

<details>
  <summary>Answer: Click me</summary>
  cmd.exe
</details>

>Tutaj pownownie wracamy do przeglądania historii przeglądarki w [[db-browser-for-sqlite]] w tabeli downloads znajdziemy nasza odpowiedź. Wygląda na to że atakujacy użył przeglądarki jak zwykłego file explorera.


---
### Q5
After downloading the file identified previously, the threat actor likely renamed it to match the allowed executable name to bypass the application restriction policy. Determining the exact timestamp of execution establishes when the threat actor gained command-line access to the system. When did the threat actor execute the downloaded file? (Format: YYYY-MM-DD HH:MM:SS)

<details>
  <summary>Answer: Click me</summary>
  2025-10-18 09:08:35
</details>

>Z pytania wiemy że atakujący pobrał narzedzie jak w Q4, zmienił go na nazwę jedynej dopuszczonej aplikacji. Z pomoca moga nam przyjść narzędzia [[pecmd]] którym sparsujemy pliki prefetch. Wyniki łatwo obejrzeć w [[timeline-explorer]].

---
### Q6
With command-line access established, the threat actor's next objective was privilege escalation. Attackers commonly download enumeration scripts to identify misconfigurations that could elevate their access. Identifying the source of this script helps map the threat actor's infrastructure and TTPs. What is the full URL from which the threat actor downloaded the privilege escalation enumeration script?

<details>
  <summary>Answer: Click me</summary>
  http://file.bsxwwdsdsa.dev/lightpeas.bat
</details>

>Atakujący miał dostep do cmd, czyli defacto mógł też powershell wywoływać. Przejrzałem dziennik ***Microsoft-Windows-PowerShell%4Operational.evtx*** ale znalazłem coś co przyda się później , nie zaś odpowiedź na pytanie. Jest jednak ciekawy artefakt wśród zabezpieczonych śladów - [[psreadline]] Zawartość mówi sama za siebie.

---
### Q7
After obtaining the local administrator credentials, the threat actor needed to spawn a new process running under the KioskAdmin security context. Identifying the exact command used reveals how the threat actor transitioned from the low-privileged kiosk user to the administrative account. What is the full command executed by the threat actor to start a new process as the KioskAdmin user?

<details>
  <summary>Answer: Click me</summary>
  runas /user:KioskAdmin powershell
</details>

>Tutaj również bazujemy jak w Q7 na pliku [[psreadline]]

---
### Qx
pytanie

<details>
  <summary>Answer: Click me</summary>
  odpowiedź
</details>

>komentarz / query

---
### Qx
pytanie

<details>
  <summary>Answer: Click me</summary>
  odpowiedź
</details>

>komentarz / query

---
### Qx
pytanie

<details>
  <summary>Answer: Click me</summary>
  odpowiedź
</details>

>komentarz / query

---

## Timeline

### 1. Initial Access

| Czas (UTC)          | Zdarzenie                                                                                                      |
| ------------------- | -------------------------------------------------------------------------------------------------------------- |
| 2025-10-18 09:05:19 | Wyskoczenie z trybu kiosku przyciskiem kombinacją klawiszy do help. Uruchamia się nowa instancja przeglądarki. |

### 2. Execution

| Czas (UTC)              | Zdarzenie                                                                                                                                                              |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2025-10-18 09:08:23<br> | Uzycie nowej instancji msedge jako file explorera i "pobranie" systemowego cmd.exe <br>Zmiana nazwy pliku na msedege.exe (jako jedynego dopuszczonego do uruchomienia) |
| 2025-10-18 09:08:35     | Uruchomienie pliku msedge.exe z loklizacji ***\Downloads***                                                                                                            |

### 3. Privilege Escalation

| Czas (UTC) | Zdarzenie |
|------------|-----------|
| | |

### 4. Credential Access
| Czas (UTC) | Zdarzenie |
|------------|-----------|
| | |

### 5. Privilege Escalation
| Czas (UTC) | Zdarzenie |
|------------|-----------|
| | |

---

## Notes
> Przeglądając logi związane z użytkownikiem zawsze należy uwzględnić czy na komputerze nie istnieją inni użytkownicy! Zawsze.

> Sztuczka w [[db-browser-for-sqlite]] na zamienianie czasu z Windows(FILETIME) na czytelny dla człowieka.
> ```SELECT datetime(13405252103670523/1000000 - 11644473600, 'unixepoch')
> ```
> czyli FILETIME dzielimy przez milion i odejmujemy stałą wartość 11644473600 (róznica między FILETIME a UNIXTIME) i zamieniamy funkcją unixepoch
> Logia jest ta sama i można implementować w innych programach i językach