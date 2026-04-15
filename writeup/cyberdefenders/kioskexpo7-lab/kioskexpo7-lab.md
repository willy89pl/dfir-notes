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

You have been provided with a KAPE([[kape]]) triage image from the compromised kiosk. Your mission is to reconstruct the complete attack timeline, identify the breakout and escalation techniques used, and determine the full scope of the compromise.

---

## Tools
| Narzędzie                 | Do czego użyte                                        |
| ------------------------- | ----------------------------------------------------- |
| [[db-browser-for-sqlite]] | czytanie historii przeglądarki                        |
| [[registry-explorer]]     | przeglądanie hive rejestrów                           |
| [[timeline-explorer]]     | przeglądanie csv sprsowanych przez toole Zimmermana   |
| [[pecmd]]                 | parser plików prefetch                                |
| [[ntfs-log-tracker]]      | pasrsowanie pllików [[mft]], [[logfile]], [[usnjrnl]] |
| [[kape]]                  | kape przygotował dane wejściowe                       |

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

>Atakujący miał dostep do cmd, czyli defacto mógł też powershell wywoływać. Przejrzałem dziennik ***Microsoft-Windows-PowerShell%4Operational.evtx*** ale znalazłem coś co przyda się później , nie zaś odpowiedź na pytanie. Jest jednak ciekawy artefakt wśród zabezpieczonych śladów - [[psreadline]] Zawartość mówi sama za siebie. Pobranie [[lightpeas]]...

---
### Q7
After obtaining the local administrator credentials, the threat actor needed to spawn a new process running under the KioskAdmin security context. Identifying the exact command used reveals how the threat actor transitioned from the low-privileged kiosk user to the administrative account. What is the full command executed by the threat actor to start a new process as the KioskAdmin user?

<details>
  <summary>Answer: Click me</summary>
  runas /user:KioskAdmin powershell
</details>

>Tutaj również bazujemy jak w Q7 na pliku [[psreadline]]

---
### Q8
Running as KioskAdmin doesn't automatically grant elevated (high integrity) privileges due to User Account Control (UAC). The threat actor would need to explicitly launch an elevated process and approve the UAC prompt. Determining when this occurred establishes the exact moment full administrative control was achieved. When did the threat actor obtain full administrative privileges by launching an elevated PowerShell process? (Format: YYYY-MM-DD HH:MM:SS)

<details>
  <summary>Answer: Click me</summary>
  2025-10-18 09:24:34
</details>

>Tutaj jest trochę trudno i jest pułapka. Musimy poszukać kilku śladów tej samej informacji i sprawdzić która jest bardziej odpowiednia. Mi pomogło prześledzenie sekwencji w dzienniku powershell-operational i obserwacje uruchomienia z prefetch. W tym drugim była prawidłowa odpowiedź.

---
### Q9
What is the name of the registry value that was set to 0 to disable User Account Control?

<details>
  <summary>Answer: Click me</summary>
  EnableLUA
</details>

>W hive ***SOFTWARE*** w ścieżce ***HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System*** znajdziemy rejestr istotny dla pytania.

---
### Q10
Before concluding the attack, the threat actor attempted to cover their tracks by tampering with evidence of commands executed under the KioskAdmin account. Identifying when this anti-forensic activity occurred helps establish the end of the active intrusion phase. When did the threat actor overwrite the PowerShell command history file to remove evidence of suspicious commands from the KioskAdmin account? (Format: YYYY-MM-DD HH:MM:SS)

<details>
  <summary>Answer: Click me</summary>
  2025-10-18 09:43:28
</details>

>Tutaj dość podchwytliwe pytanie, trzeba odnaleźć bardzo precyzyjny znacznik czasu. Próbowałem różnych ścieżek. Ostatecznie jedyna słuszna droga to przejrzeć dzienniki związane z NTFS , w ruch idzie [[ntfs-log-tracker]] i wertowanie plików [[mft]], [[logfile]], [[usnjrnl]] . Wiemy że szukamy pliku z historią komend PS, wiemy dla jakiego usera, mamy też zbudowany timeline. Wtedy jest easy.

---
### Q11
Before changing back to the registration page in restricted browser opened by kiosk, the threat actor tried to clear off the track by deleting the downloaded file identified earlier. What is the name of this file in the recycle bin?

<details>
  <summary>Answer: Click me</summary>
  $R0BD893.exe
</details>

>Wśród zabezpieczonych artefaktów przez [[kape]] jest też *kosz* ([[recycle-bin]]). Wykorzystując wiedzę o tym jak działa, o tym co znaleźliśmy w *koszu* oraz korelując to z wynikami z dzienników NTFS - odnajdziemy odpowiedź.

---
### Q12
The privilege escalation script likely discovered credentials stored insecurely in the Windows registry. What is the password for the KioskAdmin account that the threat actor retrieved from the registry?

<details>
  <summary>Answer: Click me</summary>
  KioskAdmin
</details>

>W przypadku trybu kiosk często wykonuje się autologowanie usera , a jego kredki są zahardkodowane w odpowiednim atrybucie w rejestrze.

---
### Q13
One of the scheduled tasks functioned as a beacon, periodically sending system information including the public IP address and hostname to a C2 server. Identifying which public API the script used for IP discovery helps understand the beacon's reconnaissance capabilities. What is the full URL of the public API used by the beacon script to retrieve the system's public IP address?

<details>
  <summary>Answer: Click me</summary>
  https://ipinfo.io/json
</details>

>No tutaj nauczyłem się czegoś nowego... wiemy że są skrypty .ps ,ale nie mamy ich zawartości.... Jednak wiedza o [[mft]] a przede wszystkim o plikach rezydulanych (_resident_) wszystko załatwi. Dzięki temu odzyskamy zawartość skryptów.

---
### Q14
To maintain persistent access to the compromised kiosk, the threat actor created PowerShell scripts that would be executed by scheduled tasks. Identifying where these scripts were stored helps locate the malicious payloads for further analysis. What is the full folder path where the threat actor created the PowerShell persistence scripts?

<details>
  <summary>Answer: Click me</summary>
  C:\ProgramData\Maintenance
</details>

>Wśród zabezpieczonych śladów przez [[kape]] jest folder z ***Tasks***. Wśród nich mamy dwa nietypowe taski w których zawartości widzimy ścieżkę do skryptów PS.

---

### Q15
The threat actor configured scheduled tasks to execute the persistence scripts, disguising them with legitimate-sounding names to avoid detection. Identifying these task names is essential for remediation and detecting similar compromises on other kiosk devices. What are the names of the two scheduled tasks created by the threat actor?

<details>
  <summary>Answer: Click me</summary>
  KioskStatusCheck,KioskUpdate
</details>

>Tutaj analogicznie do Q14.

---

### Q16
The second scheduled task implemented a polling mechanism to receive instructions from the C2 server. When the server responded with a specific trigger value, the script would download and execute additional payloads. What is the filename of the script that would be fetched and executed when the C2 server returned "1"?

<details>
  <summary>Answer: Click me</summary>
  quickupdate.txt
</details>

>Analogicznie do Q16. Mając zawartość skryptów możemy wyczytać zawartość do odpowiedzi.

---

### Q17
With administrative access secured, the threat actor's objective shifted to weaponizing the kiosk against conference attendees. The kiosk displayed a QR code for registration replacing it with a malicious version could redirect victims to a phishing site. Identifying the replacement file and when it was swapped is crucial for determining the window of potential victim exposure. What is the filename of the replacement QR code image, and when was it placed on the desktop? (Format: filename,YYYY-MM-DD HH:MM:SS)

<details>
  <summary>Answer: Click me</summary>
  qr.png,2025-10-18 09:30:14
</details>

>Pytanie samo sugeruje nam gdzie powinniśmy szukać. Skoro było *replacement* to i pewnie był jakiś *renaming* a więc kierujemy się do operacji wykonywanych na plikach w ***NTFS***

---

### Q18
The new QR code redirects conference participants to a phishing site instead of the legitimate registration page. What is the URL of the phishing page that is now displayed?

<details>
  <summary>Answer: Click me</summary>
  https://registerr.wowzaconf.dev/register.php
</details>

>Jednym z zabezpieczonych artefaktów przez [[kape]] jest obrazek png qr codem. znajduje się w: ***C:\Users\Administrator\Desktop\Start Here\Artifacts\KioskExpo7_Evidence\C\Users\kiosk\Desktop*** 
>Jest to podmieniony obrazek prowadzący do strony phishing,

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

| Czas (UTC)          | Zdarzenie                                                                                                                                                      |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2025-10-18 09:12:43 | Pobranie narzędzia [[lightpeas]] a w konsekwencji pozyskanie hasła do ***KioskAdmin***                                                                         |
| 2025-10-18 09:24:03 | Uruchomienie powershell jako ***KioskAdmin*** (runas)                                                                                                          |
| 2025-10-18 09:24:34 | Ponowne uruchomienie powershell już z potwierdzeniem UAC. Po tym czasie ustanowienie persystencji oraz ustanowienie kanału C2 poprzez skrypty ps w schedulrze. |
### 4. Impact

| Czas (UTC)          | Zdarzenie                                               |
| ------------------- | ---------------------------------------------------- |
| 2025-10-18 09:3 Podmiana qr-code , na qr zawierający złośliwy link.  .  .  a  |

### 5. Defense Evasion

| Czas (UTC)          | Zdarzenie                                                                                                               |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| 2025-10-18 09:43:28 | Zacieranie śladów - operacja ***Data_Overwritten*** na pliku ***ConsoleHost_history.txt*** użytkownika ***KioskAdmin*** |
| 2025-10-18 09:43:46 | Usunięcie pliku ***$R0BD893.exe*** z kosza                                                                              |


---

## Notes
> Przeglądając logi związane z użytkownikiem zawsze należy uwzględnić czy na komputerze nie istnieją inni użytkownicy! Zawsze.

> Sztuczka w [[db-browser-for-sqlite]] na zamienianie czasu z Windows(FILETIME) na czytelny dla człowieka.
> ```SELECT datetime(13405252103670523/1000000 - 11644473600, 'unixepoch')
> ```
> czyli FILETIME dzielimy przez milion i odejmujemy stałą wartość 11644473600 (róznica między FILETIME a UNIXTIME) i zamieniamy funkcją unixepoch
> Logia jest ta sama i można implementować w innych programach i językach

>W Q8 troche rozjazd bo:
>Prefetch timestamp = moment uruchomienia procesu PowerShell 40961 = moment gdy konsola jest już gotowa Różnica ~1 sekunda = normalne , i tu mi nie wchodziła odpowiedź z powershell-operational

>Q13 - no to tutaj to zwrot akcji... Bez wiedzy o systemie plików NTFS tego nie dałoby się zrobić... Pliki "rezydualne" (resident). Trzeba zapamiętać i uwzględnić w przyszłych forensic dyków z Windows....