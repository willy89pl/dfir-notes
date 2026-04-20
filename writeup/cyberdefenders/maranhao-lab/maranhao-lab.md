---
tags:
  - cyberdefenders
  - threat-hunting
platform: cyberdefenders
category: endpoint-forensic
difficulty: easy
date: 2026-04-15
---

# Maranhao Lab

> Investigate a trojanized game installer by analyzing browser history, logs, registry hives, and filesystem artifacts to map the full attack chain and extract IOCs.

## Scenario
A gaming enthusiast in a known organization has downloaded what they believed to be a free mod launcher for a popular survival game. The file which downloaded contained a ZIP archive with an installer that looked like a standard game setup package. 

Eager to try it, the gamer downloaded the file and executed the installer. Unbeknownst to him, the program silently dropped hidden files into a directory. One of these files was configured to persist through registry keys, ensuring it would relaunch every time the system started. 

Within a short time, unusual activity triggered alerts on the Security Operations Center's (SOC) in GOAT Company's monitoring dashboard. The gamer's machine was observed making outbound requests to a malicious domain and a suspicious external IP address. Endpoint logs also showed evidence of process injection, suggesting credential theft. The Security Operations Center (SOC) quickly isolated the machine and saved a full disk image for your analysis.

---

## Tools
| Narzędzie | Do czego użyte |
| --------- | -------------- |
| xxx       | yyy            |

---

## Q&A

### Q1
Analysts identified an external object that acted as the patient-zero delivery mechanism. Which remote resource URL initiated the chain of compromise by providing the archive disguised as a legitimate game utility?

<details>
  <summary>Answer: Click me</summary>
  https://drive.usercontent.google.com/uc?id=1mIxhfZXmcUT2mbKNuahsRI4S_rzVUFKW&export=download
</details>

>Interesuje nas ślad pobierania pliku, common case to przeglądarka , a więc [[db-browser-for-sqlite]] i przeglądamy plik ***history*** z edge

---
### Q2
In reconstructing the timeline of compromise, which precise timestamp correlates to the adversary's delivery vector entering the victim environment as a ZIP file?

<details>
  <summary>Answer: Click me</summary>
  2025-09-17 10:10
</details>

>Pytanie o datę pojawienia się pliku da dysku powinny od razu skierować nas do [[mft]].
>Ja tu zrobiłem trochę błąd bo najpierw dane sobie sparsowałem w NTF Log Tra

---
### Qx
pytanie

<details>
  <summary>Answer: Click me</summary>
  odp
</details>

```
koment
```

---
### Qx
pytanie

<details>
  <summary>Answer: Click me</summary>
  odp
</details>

```
koment
```

---
### Qx
pytanie

<details>
  <summary>Answer: Click me</summary>
  odp
</details>

```
koment
```

---


## Timeline

### 1. Reconnaissance
| Czas (UTC)                     | Zdarzenie                                           |
| ------------------------------ | --------------------------------------------------- |


### 2. Initial Access
| Czas (UTC)                    | Zdarzenie                                    |
| ----------------------------- | -------------------------------------------- |


### 3. Credential Access
| Czas (UTC)                     | Zdarzenie                                                            |
| ------------------------------ | -------------------------------------------------------------------- |


### 4. Privilege Escalation
| Czas (UTC)                    | Zdarzenie                                                                                                    |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------ |
