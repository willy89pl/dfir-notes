---
tags:
  - cyberdefenders
platform: cyberdefenders
category: endpoint-forensic
difficulty: medium
date:
---

# # KioskExpo7 Lab

## Scenario
On October 18, 2025, Wowza Enterprise hosted their first-ever cybersecurity conference. To streamline registration, the IT team configured several laptops in Windows Kiosk mode to display a QR-code registration page for attendee self-service.

After the event concluded, the security team detected suspicious outbound connections originating from one of the kiosk devices KioskExpo7. Surveillance footage revealed a suspicious individual spending an unusually long time interacting with that particular terminal.

Welcome to the KioskExpo7 lab! Step into the role of a forensic investigator and dissect a physical access attack that escalated from a kiosk breakout to full system compromise. The threat actor escaped browser restrictions, escalated privileges, established persistence, and potentially impacted conference attendees.

You have been provided with a KAPE triage image from the compromised kiosk. Your mission is to reconstruct the complete attack timeline, identify the breakout and escalation techniques used, and determine the full scope of the compromise.

---

## Tools
| Narzędzie | Do czego użyte |
|-----------|----------------|
| | |

---

## Q&A

### Q1
One of the most well-known kiosk breakout techniques involves abusing browser shortcuts (such as Ctrl+O, Ctrl+S, or Ctrl+P) to invoke File Explorer, then clicking the Help button to spawn an unrestricted browser instance. Determining the URL that triggered this escape is crucial for understanding the breakout vector. What is the full URL that was invoked when the threat actor clicked the Help button, allowing them to open a new browser instance outside kiosk restrictions?

<details>
  <summary>Answer: Click me</summary>
  https://go.microsoft.com/fwlink/?LinkID=2004230
</details>

```
Odpowiedzi będziemy szukać zatem w historii przeglądarki. Co ważne istotne jest aby szukać we właściwym userze. Plik histori dla Edge leży w : C:\Users\<user>\AppData\Local\Microsoft\Edge\User Data\Default\History
Przegląać to możemy np. DB browesr for SQLLite
```

---

### Q2
pytanie

<details>
  <summary>Answer: Click me</summary>
  odpowiedź
</details>

```
komentarz / query
```

---

## Timeline

### 1. Initial Access

| Czas (UTC)          | Zdarzenie                                   |
| ------------------- | ------------------------------------------- |
| 2025-10-18 09:05:19 | Wyskozenie z tryby kiosku przyciskiem help. |

### 2. Initial Access
| Czas (UTC) | Zdarzenie |
|------------|-----------|
| | |

### 3. Execution
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
> ślepe zaułki, uwagi o jakości laba, rzeczy które zaskoczyły
