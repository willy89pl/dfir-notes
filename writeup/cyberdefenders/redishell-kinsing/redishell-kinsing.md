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
  <summary>Click me</summary>
  172.16.10.10
</details>
``` Statistics > Converastion > Ipv4
Pierwsza komunikacja z zewnątrz na która natafiamy w Wireshark trafia właśnie na ten adres Ip.

