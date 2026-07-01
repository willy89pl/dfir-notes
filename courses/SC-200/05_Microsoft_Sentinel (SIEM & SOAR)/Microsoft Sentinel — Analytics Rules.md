---
tags:
  - sc-200
  - microsoft
  - sentinel
  - analytics
  - kql
  - detection
category: sc-200
---

# Microsoft Sentinel - Analytics Rules(Reguły Analityczne)

Moduł **Analytics** odpowiada za mechanizm detekcji (wykrywania) zagrożeń w Microsoft Sentinel. To tutaj definiuje się reguły, które stale przeszukują logi zgromadzone w Log Analytics Workspace przy użyciu języka KQL. W momencie, gdy zapytanie znajdzie podejrzany wzorzec (spełni warunki reguły), system generuje **Alert**, który następnie jest automatycznie grupowany w **Incydent** (Incident) trafiający do konsoli analityków SOC.

---

## Główne Typy Reguł Analitycznych

Na egzaminie SC-200 musisz precyzyjnie rozróżniać rodzaje reguł oraz wiedzieć, kiedy ich użyć:

* **Scheduled Rules (Zaplanowane):** Klasyczne i najpopularniejsze reguły tworzone ręcznie przez inżynierów SOC. Uruchamiają się cyklicznie (np. co godzinę) i wykonują zapytanie KQL (np. sprawdzają, czy w tabeli `SigninLogs` pojawiło się więcej niż 5 nieudanych logowań z różnych krajów dla jednego konta).
* **Microsoft Security Rules:** Reguły typu "passthrough". Nie wymagają pisania kodu KQL, lecz służą do automatycznego nasłuchiwania alertów z innych systemów bezpieczeństwa (np. generują incydent w Sentinel w momencie, gdy Microsoft Defender for Endpoint wykryje złośliwe oprogramowanie na stacji roboczej).
* **Fusion (Uczenie Maszynowe):** Zaawansowany, wbudowany i domyślnie włączony silnik korelacji oparty na algorytmach Machine Learning. Analizuje miliony mało istotnych alertów o niskim priorytecie (Low/Informational) rozproszonych w czasie i łączy je w jeden, krytyczny incydent wieloetapowego ataku (Multi-Stage Attack) zgodny z matrycą MITRE ATT&CK.
* **Near-Real-Time (NRT) Rules:** Specjalny typ reguł zaplanowanych, które uruchamiają się z częstotliwością co 1 minutę. Służą do wykrywania najbardziej krytycznych zagrożeń, gdzie czas reakcji musi być natychmiastowy. Mają pewne ograniczenia wydajnościowe (np. mogą przeszukiwać tylko jedną tabelę).

---

## Kluczowe Etapy Konfiguracji Reguły (Kreator Reguł)

Tworząc regułę typu *Scheduled*, konfigurujesz trzy kluczowe obszary:

1. **Set Rule Logic (Logika zapytania):** Wklejasz kod KQL, mapujesz encje (Entities, np. przypisujesz kolumnę `IPAddress` jako obiekt typu IP) oraz ustawiasz harmonogram (jak często uruchamiać zapytanie i z jakiego okresu wstecz analizować dane).
2. **Incident Configuration (Grupowanie alertów):** Decydujesz, czy każde trafienie zapytania ma tworzyć osobny incydent, czy Sentinel ma grupować alerty (np. łącz wszystkie alerty z ostatnich 5 godzin dotyczące tego samego konta użytkownika w jedno zbiorcze zgłoszenie, aby uniknąć zmęczenia alertami — *Alert Fatigue*).
3. **Automated Response (Automatyzacja):** Podpinasz reguły automatyzacji (Automation Rules) lub Playbooki, które mają się uruchomić natychmiast po wyzwoleniu tej reguły (np. automatyczna izolacja hosta).

---

> [!TIP] Wskazówka pod Twój lab i egzamin:
> Nie musisz pisać wszystkich reguł od zera. W zakładce **Analytics** ➔ **Rule templates** znajdziesz setki gotowych szablonów przygotowanych przez Microsoft i społeczność dla niemal każdego łącznika danych (w tym dla aktywowanego przed chwilą *Microsoft Entra ID*). Wystarczy wybrać szablon i kliknąć **Create rule**.