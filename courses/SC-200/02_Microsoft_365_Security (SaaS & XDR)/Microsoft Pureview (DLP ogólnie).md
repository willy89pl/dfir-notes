---
tags:
  - sc-200
  - microsoft
  - pureview
  - datasecurity
  - compliance
  - governance
category: sc-200
---

# Microsoft Purview — Charakterystyka (Pod SC-200)

**Microsoft Purview** (dawniej *Azure Purview* oraz *Microsoft 365 Compliance*) to kompleksowy parasol produktowy i platforma zarządzania danymi (Data Governance, Risk, and Compliance - GRC). Podczas gdy rodzina **Microsoft Defender** skupia się na odpieraniu ataków i ochronie infrastruktury (tożsamość, systemy, maile), **Microsoft Purview skupia się na samych danych** — na tym, gdzie się znajdują, jak są klasyfikowane i czy nie wyciekają.

W strategii **Zero Trust** Purview odpowiada za najważniejszy, wewnętrzny filar: **Dane**.



---

## Trzy Główne Obszary Microsoft Purview

Pod kątem SC-200, portfolio Purview dzieli się na trzy kluczowe filary, które musisz potrafić zidentyfikować w scenariuszach egzaminacyjnych:

### 1. Data Ochrona i Klasyfikacja (Data Security)
Narzędzia chroniące dane przed nieautoryzowanym dostępem i wyciekiem:
* **Information Protection (Sensitivity Labels):** Pozwala na nakładanie "etykiet wrażliwości" na pliki i maile (np. *Publiczny*, *Poufny*, *Ściśle Tajny*). Etykieta może automatycznie zaszyfrować plik i dodać znak wodny. Jeśli plik jest zaszyfrowany, nawet po skopiowaniu go na prywatny pendrive nikt postronny go nie otworzy.
* **Data Loss Prevention (DLP):** System zapobiegania wyciekom (omówiony wcześniej). Blokuje wysyłanie danych wrażliwych (np. numery kart, PESEL) przez kanały takie jak USB, maile, Teams czy przeglądarki internetowe.

### 2. Zarządzanie Ryzykiem Wewnętrznym (Data Risk)
* **Insider Risk Management:** Jeden z najważniejszych modułów pod SC-200. Analizuje sygnały z całego środowiska, aby wykryć złośliwe lub ryzykowne działania **własnych pracowników** (np. pracownik, który złożył wypowiedzenie, nagle zaczyna masowo pobierać pliki z SharePointa i zmieniać ich etykiety na "Publiczne").

### 3. Zgodność i Ład (Data Governance & Compliance)
* **eDiscovery (Premium):** Narzędzie dla działów prawnych do przeszukiwania całej firmy (maili, czatów Teams, plików) w celu zabezpieczenia dowodów w sprawach sądowych lub audytach.
* **Audit (Standard/Premium):** Przechowywanie i przeszukiwanie logów aktywności użytkowników i administratorów (kluczowe źródło danych dla SOC).

---

## Perspektywa SC-200: Integracja Purview z Microsoft Sentinel

Jako analityk SOC nie pracujesz na co dzień w portalu Purview, ale **konsumujesz jego logi i alerty w Microsoft Sentinel**:

1. **Korelacja alertów Insider Risk z Defenderem:** * Jeśli *Purview Insider Risk Management* oflaguje pracownika jako "wysokie ryzyko exfiltracji danych", a w tym samym czasie *Defender for Endpoint (MDE)* wykryje na jego komputerze uruchomienie podejrzanego skryptu PowerShell, Sentinel połączy to w jeden krytyczny incydent.
2. **Polowanie na zagrożenia (Threat Hunting) przez KQL:**
   * Za pomocą języka KQL przeszukujesz tabelę `OfficeActivity` lub `CloudAppEvents`, aby sprawdzić, kto i kiedy nakładał lub usuwał etykiety wrażliwości (*Sensitivity Labels*) z dokumentów przed ich pobraniem.
3. **Automatyzacja (SOAR / Playbooks):**
   * Gdy Sentinel wykryje incydent związany z wyciekiem danych z Purview DLP, automatyczny Playbook może natychmiast zablokować konto użytkownika w Entra ID oraz powiadomić dział prawny.

> [!TIP] Różnica pod egzamin (Defender vs Purview)
> * **Scenariusz:** Haker z zewnątrz próbuje włamać się na komputer ➔ Reaguje **Defender**.
> * **Scenariusz:** Pracownik legalnie otwiera poufny dokument, ale próbuje go wkleić do ChatGPT lub wynieść z firmy ➔ Reaguje **Purview**.