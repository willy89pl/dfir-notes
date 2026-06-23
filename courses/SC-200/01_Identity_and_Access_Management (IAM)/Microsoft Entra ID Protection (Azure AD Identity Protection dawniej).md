---
tags:
  - sc-200
  - microsoft
  - entraid
  - security
  - riskmanagement
category: sc-200
---

# Microsoft Entra ID Protection — Charakterystyka (Pod SC-200)

**Microsoft Entra ID Protection** (dawniej *Azure AD Identity Protection*) to moduł bezpieczeństwa premium (wymaga licencji **Entra ID P2 / M365 E5**), który pełni rolę inteligentnego systemu detekcji anomalii dla tożsamości chmurowych. Wykorzystuje zaawansowane algorytmy uczenia maszynowego Microsoftu do analizowania miliardów logowań dziennie, automatycznie wykrywając, oceniając i mitygując ryzyka związane z kontami użytkowników.

Działa jako główny "silnik oceny ryzyka" dla polityk Dostępu Warunkowego (*Conditional Access*).

---

## Dwa Filary Oceny Ryzyka (Risk Types)

Na egzaminie SC-200 musisz bezbłędnie rozróżniać te dwa typy ryzyka, ponieważ konfiguruje się dla nich osobne reakcje obronne:

### 1. Sign-in Risk (Ryzyko Logowania)
* **Definicja:** Prawdopodobieństwo, że konkretna, pojedyncza próba uwierzytelnienia nie została wykonana przez prawowitego właściciela konta. Sprawdza anomalie "tu i teraz".
* **Kluczowe detekcje (Anomalie):**
  * **Impossible Travel (Niemożliwa podróż):** Logowanie z Warszawy, a 10 minut później z Tokio (fizycznie niemożliwe).
  * **Unfamiliar sign-in properties:** Logowanie z nietypowej przeglądarki, nowego urządzenia lub z lokalizacji, z której dany użytkownik nigdy wcześniej nie pracował.
  * **Anomalous Token / Token Theft:** Wykrycie anomalii w tokenie sesyjnym (podejrzenie ataku *Session Hijacking* / AiTM).
  * **Malware linked IP address:** Logowanie z adresu IP należącego do botnetu lub zainfekowanego węzła.

### 2. User Risk (Ryzyko Użytkownika)
* **Definicja:** Prawdopodobieństwo, że tożsamość/konto jako całość zostało skompromitowane, a jego poświadczenia (login i hasło) wpadły w ręce przestępców. Dotyczy permanentnego stanu konta, a nie jednego logowania.
* **Kluczowe detekcje (Anomalie):**
  * **Leaked Credentials (Wyciek poświadczeń):** Microsoft stale skanuje publiczne wycieki, fora hakerskie i darknet. Jeśli znajdzie tam parę login:hasło Twojego pracownika, natychmiast podnosi *User Risk* do poziomu *High*.
  * **Microsoft Defender for Identity signals:** Alerty z lokalnego Active Directory (np. wykrycie ataku typu *Pass-the-Hash* na tym użytkowniku) automatycznie podnoszą jego poziom ryzyka w chmurze.

---

## Poziomy Ryzyka i Automatyczna Mitygacja (Remediation)

Zarówno *User Risk*, jak i *Sign-in Risk* są klasyfikowane w trzystopniowej skali: **Low**, **Medium**, **High**.

W połączeniu z **Dostępem Warunkowym (Conditional Access)**, Entra ID Protection pozwala na automatyczne reagowanie (Self-Healing) bez udziału analityka SOC:

* **Wykryto Sign-in Risk (poziom Medium/High):** Polityka wymusza natychmiastowe przejście wieloskładnikowego uwierzytelnienia (MFA).
* **Wykryto User Risk (poziom High):** Polityka blokuje dostęp do odwołania LUB wymusza bezpieczny reset hasła poprzez procedurę SSPR (Self-Service Password Reset).

---

## Perspektywa SC-200: Dochodzenie w SOC (Sentinel & KQL)

Wszystkie alerty i ryzyka wygenerowane przez ten moduł trafiają do portalu Microsoft Defender XDR oraz bezpośrednio do **Microsoft Sentinel**.

Podczas analizy incydentów (Threat Hunting), analityk SOC wykorzystuje KQL do przeszukiwania ryzykownych zachowań:

* `SigninLogs`: Główna tabela logów logowań. Szukaj w niej kolumn `RiskLevelDuringSignIn`, `RiskLevelAfterSignIn`, `RiskState` oraz `RiskDetail`.
* `UserRiskEvents` / `AADUserRiskEvents`: Tabele dedykowane samym zdarzeniom ryzyka użytkowników, pozwalające wyciągnąć informacje o tym, jakie dokładnie zachowanie (np. *impossible travel*) wyzwoliło alert.

> [!TIP] Złota zasada pod egzamin:
> Jeśli pytanie scenariuszowe wspomina o **wykrywaniu wycieków haseł w darknecie**, anomalii typu **Impossible Travel** lub dynamicznym wymuszaniu MFA w zależności od **poziomu ryzyka (Risk Level)** — właściwym narzędziem zawsze jest **Microsoft Entra ID Protection**.