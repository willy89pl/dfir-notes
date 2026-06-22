---
tags:
  - sc-200
  - microsoft
  - cloud
  - m365
  - defender
  - saas
category: sc-200
---

# Microsoft 365 (M365) — Charakterystyka (Pod SC-200)

**Microsoft 365 (M365)** to flagowy pakiet usług chmurowych (model **[[SaaS]]** ) od Microsoftu, który łączy w sobie aplikacje biurowe (Productivity Apps), system operacyjny Windows 11 Enterprise oraz zaawansowane usługi chmurowe do współpracy, takie jak **Exchange Online** (poczta), **SharePoint Online**, **OneDrive for Business** oraz **Microsoft Teams**.

Z punktu widzenia SC-200, M365 to potężne środowisko, które generuje ogromną ilość wrażliwych danych i jest **głównym celem ataków phishingowych oraz socjotechnicznych**.

---

## Architektura Bezpieczeństwa: Microsoft 365 Defender XDR

W agendzie SC-200 ochrona środowiska M365 opiera się na pakiecie narzędzi **XDR (Extended Detection and Response)**, znanym jako **Microsoft Defender XDR** (dawniej *Microsoft 365 Defender*). Tworzą go cztery kluczowe filary:

### 1. Microsoft Defender for Office 365 (MDO)
Chroni narzędzia do komunikacji i współpracy przed zaawansowanymi zagrożeniami (malware, phishing, spam).
* **Główne funkcje pod egzamin:**
    * **Safe Attachments (Bezpieczne załączniki):** Otwiera załączniki z maili w odizolowanym środowisku (sandbox), aby sprawdzić, czy nie zachowują się jak wirus, zanim trafią do skrzynki użytkownika.
    * **Safe Links (Bezpieczne linki):** Skanuje i podmienia linki w mailach i na Teams. Sprawdza stronę www w momencie, gdy użytkownik w nią klika (ochrona w czasie rzeczywistym przed złośliwymi witrynami).

### 2. Microsoft Defender for Endpoint (MDE)
Chmurowe rozwiązanie klasy **EDR** do ochrony urządzeń końcowych (laptopy, telefony, serwery). Wykrywa anomalie w zachowaniu systemów, procesów i blokuje ataki typu ransomware bezpośrednio na komputerze pracownika.

### 3. Microsoft Defender for Identity (MDI)
Monitoruje lokalne kontrolery domeny (Active Directory) i wykrywa próby kradzieży tożsamości wewnątrz sieci firmowej.

### 4. Microsoft Defender for Cloud Apps (MDCA)
Rozwiązanie klasy **CASB (Cloud Access Security Broker)**. Działa jak "pośrednik" i strażnik ruchu między Twoimi użytkownikami a aplikacjami chmurowymi (zarówno Microsoftu, jak i zewnętrznymi, np. Salesforce, Dropbox). Wykrywa anomalie, np. masowe pobieranie danych z OneDrive przez pracownika, który zaraz odchodzi z firmy.

---

## Portal Microsoft Defender (security.microsoft.com)

Jako analityk SOC, na egzaminie SC-200 będziesz testowany z umiejętności poruszania się po ujednoliconym portalu bezpieczeństwa M365.



* **Incidents & Alerts (Incydenty i Alerty):** Portal automatycznie łączy pojedyncze alerty z różnych narzędzi (np. mail z phishingiem z MDO + uruchomienie podejrzanego procesu na komputerze z MDE) w jeden spójny **Incydent**, pokazując pełną oś czasu ataku (Attack Story).
* **Action Center (Centrum akcji):** Miejsce, w którym zatwierdzasz automatyczne lub ręczne działania naprawcze (Remediation Actions), np. poddanie pliku kwarantannie, izolacja zainfekowanego komputera od sieci czy zablokowanie konta użytkownika.
* **Hunting (Zaawansowane wyszukiwanie):** Sekcja, w której używasz języka **KQL (Kusto Query Language)** do przeszukiwania logów z całego środowiska M365 w poszukiwaniu ukrytych śladów włamań.

---

## Model Współdzielonej Odpowiedzialności w M365
Ponieważ M365 to czysty SaaS:
* **Microsoft odpowiada za:** Działanie serwerów pocztowych Exchange, infrastruktury Teams i fizyczne bezpieczeństwo baz danych.
* **Ty odpowiadasz za:** To, komu dajesz uprawnienia administratora, czy wymuszasz MFA dla użytkowników oraz jak skonfigurujesz polityki anty-phishingowe w Defenderze.