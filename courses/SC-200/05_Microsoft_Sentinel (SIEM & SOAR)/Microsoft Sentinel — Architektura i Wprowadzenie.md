---
tags:
  - sc-200
  - microsoft
  - sentinel
  - siem
  - soar
category: sc-200
---
# Microsoft Sentinel — Architektura i Wprowadzenie

Microsoft Sentinel to chmurowe rozwiązanie klasy **SIEM** (Security Information and Event Management) oraz **SOAR** (Security Orchestration, Automation, and Response). Jest to serce nowoczesnego centrum operacji bezpieczeństwa (SOC) w ekosystemie Microsoft, które agreguje logi z całej infrastruktury (chmurowej i lokalnej), wykrywa zagrożenia przy użyciu analityki AI oraz automatyzuje proces reagowania na incydenty.

W przeciwieństwie do klasycznych systemów SIEM, Sentinel jest usługą w pełni natywną dla chmury (Cloud-Native), co oznacza brak konieczności zarządzania infrastrukturą serwerową czy przestrzenią dyskową. Skaluje się automatycznie i bazuje na repozytorium **Log Analytics Workspace**.

---

## Cztery Filary Działania Sentinel

Praca w systemie Microsoft Sentinel (oraz pytania na egzaminie SC-200) kręci się wokół czterech głównych etapów cyklu życia danych bezpieczeństwa:

1. **Collect (Zbieranie):** Gromadzenie danych ze wszystkich użytkowników, urządzeń, aplikacji i infrastruktur (zarówno z chmury Azure/M365, jak i od zewnętrznych dostawców np. AWS, Cisco, Fortinet).
2. **Detect (Wykrywanie):** Analizowanie zebranych logów pod kątem anomalii i zagrożeń za pomocą reguł analitycznych (Analytics Rules) oraz wbudowanych mechanizmów Machine Learningu.
3. **Investigate (Dochodzenie):** Badanie wygenerowanych incydentów, przeszukiwanie logów (Threat Hunting) za pomocą języka KQL oraz wizualizacja powiązań między alertami na grafie dochodzeniowym.
4. **Respond (Reagowanie):** Automatyczna mitygacja zagrożeń za pomocą scenariuszy (Playbooks) i reguł automatyzacji, co pozwala odciążyć analityków SOC od powtarzalnych zadań.

---

## Główne Moduły Systemu (Perspektywa SC-200)

Aby skutecznie zarządzać platformą, Sentinel został podzielony na dedykowane moduły funkcjonalne:

* **Data Connectors (Łączniki danych):** Moduł odpowiedzialny za integrację i strumieniowanie logów ze źródeł zewnętrznych.
* **Analytics (Analityka):** Miejsce tworzenia i zarządzania regułami, które przekształcają surowe logi w alerty i incydenty.
* **Incidents (Incydenty):** Główna konsola operacyjna analityka SOC służąca do zarządzania cyklem życia wykrytych ataków.
* **Hunting (Polowanie na zagrożenia):** Narzędzie do proaktywnego wyszukiwania śladów kompromitacji (IoC) przed wyzwoleniem oficjalnego alertu.
* **Workbooks (Skoroszyty):** Moduł wizualizacji danych i tworzenia dynamicznych dashboardów operacyjnych.
* **Automation (Automatyzacja):** Serce komponentu SOAR, łączące reguły automatyzacji z playbookami opartymi na Azure Logic Apps.

---

> [!TIP] Podstawa architektury pod egzamin:
> Pamiętaj, że Microsoft Sentinel nie przechowuje danych "sam w sobie". Każde wdrożenie Sentinela musi być powiązane z **Log Analytics Workspace (LAW)**. To wewnątrz LAW definiuje się retencję danych (czas przechowywania) oraz ponosi koszty za wolumen zaciąganych logów (Ingestion).