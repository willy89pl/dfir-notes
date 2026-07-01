---
tags:
  - sc-200
  - microsoft
  - sentinel
  - reporting
  - workbooks
  - visualization
category: sc-200
---

# Microsoft Sentinel Moduł: Workbooks

Moduł **Workbooks**  to natywne narzędzie w Microsoft Sentinel służące do wizualizacji danych, raportowania oraz tworzenia interaktywnych dashboardów. Bazuje ono bezpośrednio na usłudze **Azure Monitor Workbooks**. 

W środowisku SOC Workbooks pozwalają przekształcić miliony surowych linii logów zapisanych w języku KQL w czytelne wykresy, mapy, tabele i wskaźniki KPI.

---

## Kluczowe Cechy i Funkcje Workbooks

Podczas nauki do egzaminu SC-200 musisz pamiętać o następujących właściwościach tego modułu:

* **Praca w czasie rzeczywistym:** Wykresy i panele nie są statycznymi grafikami. Pobierają one najświeższe dane z Log Analytics Workspace przy każdym odświeżeniu lub otwarciu workbook.
* **Interaktywność:** Kliknięcie elementu na jednym wykresie (np. wybranego użytkownika na wykresie słupkowym) może automatycznie przefiltrować pozostałe wykresy i tabele w całym dokumencie do danych powiązanych tylko z tym użytkownikiem.
* **Gotowe szablony (Templates):** Microsoft oraz społeczność dostarczają setki gotowych skoroszytów dla konkretnych technologii (np. dashboard monitorujący bezpieczeństwo *Microsoft Entra ID*, aktywność *Office 365* czy logi z zapór *Palo Alto* / *Cisco*).

---

## Zastosowanie w Architekturze SOC

Skoroszyty w codziennej pracy inżynierów bezpieczeństwa pełnią trzy główne role:

1. **Wizualizacja Incydentów i KPI (Dla Kierownictwa):** Tworzenie raportów pokazujących np. ile incydentów wykryto w tym miesiącu, jaki był średni czas reakcji (MTTR) oraz które stacje robocze generowały najwięcej alertów.
2. **Narzędzie Pomocnicze do Dochodzeń (Dla Analityków):** Dedykowane szablony (np. *User Identity Investigation*) pozwalają analitykowi wpisać login użytkownika, a Workbook automatycznie generuje zestaw wykresów pokazujący jego geograficzną lokalizację logowań, używane urządzenia i anomalie z ostatnich 14 dni.
3. **Monitorowanie Zdrowia Systemu (Dla Inżynierów):** Skoroszyt *Data Collectors Health Monitor* pozwala sprawdzać, czy łączniki danych (Data Connectors) działają poprawnie i czy nagle nie spadł wolumen zaciąganych logów, co mogłoby świadczyć o awarii agenta AMA.([[Azure Monitor Agent (AMA) Connector]])

---

> [!TIP] Rola do modyfikacji (Pułapka SC-200)
> Do samego przeglądania i korzystania z gotowych zapisanych Skoroszytów wystarczy podstawowa rola **Microsoft Sentinel Reader**. Jednak jeśli analityk ma za zadanie edytować kod KQL stojący za wykresami lub zapisać nowy, niestandardowy dashboard, musi posiadać uprawnienia minimum **Microsoft Sentinel Contributor** (lub ogólną rolę *Workbook Contributor* na poziomie grupy zasobów).