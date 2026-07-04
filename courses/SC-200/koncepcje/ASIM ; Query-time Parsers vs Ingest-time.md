---
tags:
  - sc-200
  - microsoft
  - sentinel
  - asim
  - kql
category: sc-200
---

# ASIM: Query-time Parsers vs Ingest-time

W architekturze ASIM wyróżniamy dwa podejścia do normalizacji danych. Standardem i domyślnym wyborem w Microsoft Sentinel są **Query-time Parsers**.

---

## Czym są Query-time Parsers?

To parsery zaimplementowane jako **funkcje KQL**. Dane w Log Analytics Workspace są przechowywane w swojej surowej, oryginalnej formie (np. w tabeli `CommonSecurityLog` lub `Syslog`). 

Proces mapowania i wyciągania zmiennych do standardu ASIM (np. przekształcenie kolumny `deviceAddress` w `SrcIpAddr`) zachodzi **dynamicznie, w pamięci, dopiero w momencie gdy użytkownik lub reguła analityczna uruchomi zapytanie**.

* **Jak to działa w kodzie:** Wywołanie parsera `imNetworkSession` uruchamia pod spodem ukrytą funkcję KQL, która w locie nakłada strukturę ASIM na surowe tabele.

---

## Druga strona medalu: Ingest-time Parsers

Dla kontrastu, **Ingest-time Parsers** modyfikują i normalizują dane **zanim** zostaną one zapisane na dysku (za pomocą reguł DCR - Data Collection Rules). Dane trafiają do bazy już w znormalizowanej strukturze.

### Szybkie porównanie (Ściąga pod egzamin)

| Cecha | Query-time Parsers (Domyślne) | Ingest-time Parsers |
| :--- | :--- | :--- |
| **Gdzie zachodzi transformacja?** | W locie, podczas uruchamiania zapytania KQL. | Podczas wciągania danych do bazy (DCR). |
| **Stan surowych logów** | Nienaruszony (idealny do analizy śledczej). | Zmodyfikowany/Zastąpiony przez standard ASIM. |
| **Wydajność zapytania KQL** | Może być wolniejszy przy gigantycznych bazach (bo filtruje w locie). | Bardzo szybki (dane są już idealnie poukładane na dysku). |
| **Elastyczność** | Wysoka (zmiana kodu parsera naprawia analizę historycznych danych). | Niska (błędna reguła uszkodzi logi bezpowrotnie przy zapisie). |

---

> [!IMPORTANT] Co zapamiętać pod SC-200?
> Jeśli w pytaniu pojawi się kwestia wydajności i optymalizacji kosztów/czasu wyszukiwania przy ogromnych wolumenach danych (Petabajty logów), rozwiązaniem optymalnym wydajnościowo staje się **Ingest-time**. Jeśli kluczowa jest elastyczność i zachowanie oryginalnego formatu logów dla audytorów, właściwą odpowiedzią jest **Query-time**.