---
tags:
  - active-directory
  - sc-200
  - sentinel
  - datacollection
  - ama
category: sc-200
---
# Azure Monitor Agent (AMA) Connector

**Azure Monitor Agent (AMA)** to oficjalny, zunifikowany agent Microsoftu zastępujący starsze rozwiązanie Log Analytics Agent (MMA/OMS). W systemie Microsoft Sentinel służy do zbierania logów zdarzeń systemowych, logów zabezpieczeń (Security Events), logów aplikacji, a także strumieni Syslog i CEF z maszyn Windows oraz Linux (wirtualnych i fizycznych).

---

## Serce systemu: Data Collection Rules (DCR)

Najważniejszą cechą konektorów opartych na AMA jest pełna integracja z **Data Collection Rules (DCR)**. DCR to reguła definiowana scentralizowanie w chmurze Azure, która określa:
* **Co zbierać:** Dokładne filtrowanie logów (np. za pomocą zapytań XPath dla Windows Event Log).
* **Skąd zbierać:** Wskazanie konkretnych maszyn lub całych grup zasobów.
* **Gdzie wysyłać:** Wskazanie docelowego Log Analytics Workspace powiązanego z Sentinel.

Dzięki DCR analityk SOC ma pełną kontrolę nad wolumenem danych, filtrując zbędny szum i zmniejszając koszty licencyjne ingestion bezpośrednio na poziomie systemu operacyjnego hosta.

---

## Rola Azure Arc dla systemów On-Premises

Podczas analizy scenariuszy hybrydowych na egzaminie SC-200 pamiętaj o złotej ścieżce dla serwerów poza chmurą Azure:
1. Serwer lokalny (np. w fizycznym centrum danych) musi zostać zarejestrowany w usłudze **Azure Arc**.
2. Azure Arc mapuje ten serwer jako natywny zasób w Twojej grupie zasobów Azure (Resource Group).
3. Na tak przygotowany zasób Arc wdraża się agenta **AMA** i przypisuje regułę **DCR**.

> [!CAUTION] Pułapka SC-200 (Wycofanie MMA)
> Starszy agent (Log Analytics Agent / MMA) został oficjalnie wycofany przez Microsoft. Wszelkie pytania projektowe i wdrożeniowe dotyczące zbierania logów z maszyn systemowych w nowoczesnym SOC muszą wskazywać na użycie **Azure Monitor Agent (AMA)** i reguł **DCR**.