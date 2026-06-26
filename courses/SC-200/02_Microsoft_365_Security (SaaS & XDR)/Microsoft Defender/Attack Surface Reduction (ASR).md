---
tags:
  - sc-200
  - microsoft
  - defender
  - endpoint
  - asr
  - hardening
category: sc-200
---

# Attack Surface Reduction (ASR) — Reguły Zmniejszania Powierzchni Ataku

**Attack Surface Reduction (ASR)** to zestaw zaawansowanych reguł wbudowanych w Microsoft Defender for Endpoint (MDE) i antywirus Windows Defender. Ich zadaniem jest blokowanie najpopularniejszych zachowań i wektorów ataku wykorzystywanych przez malware oraz hakerów na etapie infekcji wstępnej (Initial Access).

ASR zamyka luki w aplikacjach, zanim napastnik zdąży je wykorzystać — blokuje m.in. uruchamianie podejrzanych skryptów, makr czy podejrzane interakcje między procesami.

---

## Trzy Tryby Działania Reguł ASR

Podczas wdrażania ASR w organizacji (poprzez Intune lub GPO) każdą regułę można ustawić w jednym z trzech trybów:

1. **Audit (Audyt):** Reguła nie blokuje żadnego działania. Jeśli dojdzie do podejrzanego zachowania, system jedynie zapisze zdarzenie w logach. Służy do testowania wpływu reguł na aplikacje biznesowe przed ich wdrożeniem.
2. **Block (Blokowanie):** Reguła aktywnie blokuje złośliwe zachowanie (np. nie pozwala Wordowi na odpalenie skryptu PowerShell). Użytkownik widzi powiadomienie systemowe, a do SOC trafia alert.
3. **Warn (Ostrzeżenie):** Reguła informuje użytkownika, że zachowanie jest ryzykowne, ale pozwala mu kliknąć "Zezwól" i kontynuować (niedostępne dla niektórych reguł chroniących pamięć LSASS).

---

## Kluczowe Kategorie Reguł (Matryca SC-200)

Reguły ASR dzielą się na kilka głównych obszarów, które musisz kojarzyć na egzaminie:

* **Reguły dotyczące pakietu Office:** Blokowanie tworzenia procesów potomnych przez aplikacje Office (klasyczny wektor phishingowy), blokowanie wstrzykiwania kodu przez Office do innych procesów.
* **Reguły dotyczące skryptów:** Blokowanie uruchamiania złośliwych skryptów obfuscated (Zaciemniony kod), blokowanie uruchamiania skryptów VBScript/JavaScript pobranych z internetu.
* **Reguły systemowe (Credentials Protection):** Krytyczna reguła pod SOC: "Block credential stealing from the Windows local security authority subsystem (lsass.exe)". Blokuje próby zrzucania pamięci LSASS (np. przez narzędzie Mimikatz) w celu kradzieży haseł.

---

## Analiza i Polowanie w SOC (KQL)

Gdy reguła ASR zablokuje działanie lub zadziała w trybie audytu, dane trafiają do zunifikowanej tabeli w Microsoft Sentinel i Defender XDR.

Główna tabela do analizy zdarzeń ASR to **DeviceEvents**. Do filtrowania używa się kolumny `ActionType`.

Przykładowe zapytanie KQL:
> DeviceEvents
> | where ActionType startswith "Asr"
> | project TimeGenerated, DeviceName, ActionType, FileName, FolderPath, InitiatingProcessFileName

---

> [!TIP] Złota zasada wdrożenia pod egzamin:
> Zawsze wdrażaj reguły ASR najpierw w trybie **Audit**. Pozwala to przeanalizować w KQL, czy reguła nie zablokuje legalnych skryptów używanych przez wewnętrzne systemy IT (tzw. False Positives), a następnie dodać odpowiednie wykluczenia (Exclusions) przed przełączeniem w tryb **Block**.