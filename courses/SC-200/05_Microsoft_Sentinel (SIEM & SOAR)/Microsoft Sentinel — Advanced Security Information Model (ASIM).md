---
tags:
  - sc-200
  - microsoft
  - sentinel
  - asim
  - normalization
  - kql
category: sc-200
---
# Microsoft Sentinel — Advanced Security Information Model (ASIM)

**ASIM** to mechanizm **normalizacji danych** w Microsoft Sentinel. Tłumaczy niestandardowe logi z różnych systemów (Cisco, Fortinet, Windows itp.) do jednego wspólnego standardu nazw kolumn.

---

## Komponenty ASIM

1. **Schematy (Schemas):** Odgórnie zdefiniowane szablony tabel dla konkretnych typów aktywności (np. *Network Session*, *Web Session*, *DNS Activity*). Określają sztywne nazwy parametrów (np. adres źródłowy to zawsze `SrcIpAddr`).
2. **Parsery (Funkcje KQL):** Translatory wdrożone w Sentinel, które w locie mapują surowe, producenckie nazwy kolumn (np. `client_ip`, `SrcIP`) na nazwy zgodne z oficjalnym schematem.

---

## Przykład użycia w KQL

Zamiast odpytywać surowe tabele poszczególnych producentów, w zapytaniach wykorzystuje się ujednolicony parser (zapisany poniżej bez znaków specjalnych):

> imNetworkSession | where SrcIpAddr == "192.168.1.50"

*To jedno zapytanie przeszuka logi sieciowe ze wszystkich zintegrowanych urządzeń (niezależnie od marki), ponieważ ASIM znormalizował dane źródłowe.*

---

## Kluczowe zalety (Wymagania SC-200)

* **Vendor-agnostic (Niezależność od producenta):** Reguły analityczne KQL działają bez zmian, nawet jeśli organizacja wymieni sprzęt sieciowy lub oprogramowanie na rozwiązania innej firmy.
* **Integracja z Content Hub:** Gotowe reguły i dashboardy (Workbooks) oparte na ASIM działają natychmiast po podłączeniu kompatybilnych łączników danych.
* **Identyfikacja w kodzie:** Wbudowane parsery ASIM łatwo rozpoznać w KQL po prefiksach takich jak **im** (np. `imDns`, `imWebSession`) lub **as_**.