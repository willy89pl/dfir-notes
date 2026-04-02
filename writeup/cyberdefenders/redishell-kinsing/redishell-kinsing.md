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
  xxx
</details>

```
xxx
```

#### Qx
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```

#### Qx
After confirming code execution, the attacker established a reverse shell connection back to their C2 infrastructure. What port number did the attacker use for the initial reverse shell listener?
<details>
  <summary>Answer: Click me</summary>
  xxx
</details>

```
xxx
```