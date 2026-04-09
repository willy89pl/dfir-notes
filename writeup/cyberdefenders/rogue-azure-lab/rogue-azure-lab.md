# Rogue Azure Lab
Reconstruct a multi-stage Azure attack timeline by analyzing Entra ID, Audit, and Storage Blob logs using Kusto Query Language to identify initial access, persistence, privilege escalation, and data exfiltration.

## Scenario
On November 14, 2025, security monitoring detected suspicious authentication activity in the Azure tenant, with anomalous sign-in patterns from multiple geographic locations. Shortly after, automated alerts flagged unauthorized administrative actions and configuration changes within the environment.


You have been provided with Azure sign-in logs, audit logs, and storage access logs from the affected tenant. Your mission is to investigate the incident, determine how the attacker gained initial access, identify what persistence mechanisms were established, document any privilege changes, and confirm whether sensitive data was accessed or exfiltrated.

## Tools
Paca bedzie odbywać się w narzędziach MS:
Microsoft Sentinel
Azure Monitor
KQL Query Editor
Azure AD Sign-in Logs

## Q&A

#### Q1
The investigation begins by analyzing a password spray attack that targeted several users in the primary tenant. What IP address did the attacker originate the password spray attack from?
<details>
  <summary>Answer: Click me</summary>
  52.59.240.166
</details>

```
Najważniejsze to dobrze zaczac... Są o logi custom, więcej trzeba najpierw przyjrzeć się zawartośi kolumn, wyszukac interesujace wskażniki.
Ciekawe zapytanie które nam może pomóc zbudowane poniżej: szuka nieudanych logowań , grupujac po adresie ip, i zliaczajac to w okresach 5min. Operujac odpowiednio oknem czasu i ilością nieudanych prób można uzyskac ciekawy i przejrzysty efekt. Mylące jest rochę pytanie o password sprawy bo nie do końca to odpowiada typowemy password spray
```
```sql
InteractiveSignIns_CL
| where Status == "Failure"
| summarize 
    FailedAttempts = count(),
    UniqueUsers = dcount(Username)
by IPAddress, bin(EventTime, 5m)
| where FailedAttempts > 1 and UniqueUsers > 1
| order by EventTime desc
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