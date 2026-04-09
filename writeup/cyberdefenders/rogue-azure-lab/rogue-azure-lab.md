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
Najważniejsze to dobrze zaczac... I w których logach szukać (InteractiveSignIns_CL) Są o logi custom, więcej trzeba najpierw przyjrzeć się zawartośi kolumn, wyszukac interesujace wskażniki.
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

#### Q2
After numerous failed attempts, the attacker successfully gained access to an account. What is the username of the first account that was compromised?
<details>
  <summary>Answer: Click me</summary>
  mharmon@compliantsecure.store
</details>

```
Znając adres IP możemu łatwo wyfiltrować 
```

#### Q3
Following the initial compromise, the attacker began using a new infrastructure for post-exploitation activities. What is the second IP address used by the attacker?
<details>
  <summary>Answer: Click me</summary>
  52.221.180.165
</details>

```
Sledząć logowania usera z Q2 nastepne udane logowanie pochodzi z nowej loklizacji i adresu IP.
```

#### Q4
From which country did the successful sign-in originate when the attacker pivoted to their secondary infrastructure for post-exploitation activities?
<details>
  <summary>Answer: Click me</summary>
  Singapore
</details>

```
Analogicznie do Q3 to po prostu szczegóły z tego eventu.
```

#### Q5
To establish persistence, the attacker registered malicious applications. What is the name of the first application they created?
<details>
  <summary>Answer: Click me</summary>
  OfficeRead
</details>

```
Aby odpowiedzien na to pytanie musimy przenieść się do kolejnych logów(AuditLogs_CL), znając czas logowania , oraz adres IP adwersarza możemy odnaleść Activity: Create application – Certificates and secrets management. Tam w customowymn polu będzie nazwa aplikacji.
```

#### Q6
The attacker created a second application to ensure persistent access, this one intended to access directory information. What is the name of this second application?
<details>
  <summary>Answer: Click me</summary>
  VaultApp
</details>

```
Analogicznie do Q5, jest to kolejny event tego typu.
```

#### Q7
To create a redundant backdoor, the attacker used the compromised administrator account to elevate the privileges of another user. What is the User Principal Name of the account that had its privileges escalated?
<details>
  <summary>Answer: Click me</summary>
  lwilliams@compliantsecure.store
</details>

```
Podążajać za kolejnymi czynnościami w AuditLogs_CL wykonywanymi z namierzonego IP zauwązymy Activity: Add member to role. W szczegółach eventu jst odpowiedź.
```

#### Q8
What highly privileged role was assigned to the second user account to grant it administrative control over the tenant?
<details>
  <summary>Answer: Click me</summary>
  Global Administrator
</details>

```
Analogicznie do Q7.
```

#### Q9
The attacker's final objective was data exfiltration. They targeted a specific storage resource to access sensitive files. What is the name of the storage account they accessed?
<details>
  <summary>Answer: Click me</summary>
  mainstoragestore01
</details>

```
Aby odpowiedzieć na tą część musimy sie tym razem przenieść do kolejnej tabeli z logami (StorageBlobLogs_CL). Tutaj znajdziemy logi dostępu do plików.
Jako że są to logi custom, informacje sa nieco inne niż w typowej tabeli. nie ma wprost adresów ip tylko cały socket (ip:port).
Poniżej proste zapytanie którym wyfiltrujemy logi do operacji wykonanych z ip adwersarza. Przy OperationName: GetBlob znajdziemy odpowiedź.

```
```sql
StorageBlobLogs_CL
|extend ip = split(CallerIpAddress, ":")[0]
|where  tostring(ip) == "52.221.180.165"
```

#### Q10
The attacker successfully downloaded a sensitive file from the storage account. What is the name of the exfiltrated file?
<details>
  <summary>Answer: Click me</summary>
  Confidintal.png
</details>

```
Analogicznie do Q9, w Uri zawarta jest odpowiedź.
```


## tl;dr czyli Kill Chain
1. Initial Access & Reconnaissance
  - password spraying na 2 userów
  - udane zalogowanie na mharmon@compliantsecure.store [2025-11-14T21:56:25Z]

2. Command & Control (C2)
  - logowanie na przejętych poświadczeniach z nowej lokalizacji (Singapore, 52.221.180.165), [2025-11-14T23:34:34Z]

3. Persistence
  - utworzenie aplikacji (OfficeRead[2025-11-14T23:35:14.961145Z], VaultApp[2025-11-14T23:35:52.755978Z])

4. Privilege Escalation
  - podniesienie uprawnień na Global Administrator dla kolejnego usera (lwilliams@compliantsecure.store) [2025-11-14T23:38:03.510353Z]

5. Collection & Exfiltration
  - Wykonanie operacji pobrania https://mainstoragestore01.blob.core.windows.net:443/conf-images/Confidintal.png [brak znacznika czasu w logach]

# Lessons&Learned

| Tabela | Co zawiera | Do czego używać | Przykładowe pola |
|------|-----------|----------------|------------------|
| InteractiveSignIns_CL | Logowania użytkowników (interaktywne) | Wykrywanie brute force, podejrzanych logowań, MFA bypass | Username, IPAddress, Status, EventTime, UserAgent |
| AuditLogs_CL | Zmiany w Azure AD / działania administracyjne | Wykrywanie zmian uprawnień, tworzenia aplikacji, consentów | Activity, Result, ActorUserPrincipalName, IPAddress |
| StorageBlobLogs_CL | Dostęp do plików w Azure Storage | Wykrywanie data exfiltration, masowego pobierania danych | OperationName, CallerIpAddress, RequesterUpn, Uri, StatusText |


Blob (Binary Large Object) - czyli zasadniczo każdy plik w storage