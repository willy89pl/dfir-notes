---
tags:
  - sc-200
  - microsoft
  - sentinel
  - dataingestion
category: sc-200
---

# Microsoft Sentinel Moduł: Data Connectors

Moduł **Data Connectors** (Łączniki danych) to punkt wejścia dla wszystkich informacji trafiających do Microsoft Sentinel. Bez skonfigurowania łączników system pozostaje "pusty" i nie ma możliwości analizowania ruchu ani wykrywania zagrożeń.

---

## Typy Łączników i Metody Integracji

Na egzaminie SC-200 kluczowa jest wiedza, jak poszczególne systemy dostarczają logi do chmury. Wyróżniamy trzy główne architektury połączeń:

### 1. Integracja Service-to-Service (Natywna)
Najprostszy typ połączenia, realizowany wewnątrz chmury Microsoft. Logi z usług takich jak Microsoft Entra ID, Microsoft Defender XDR czy Azure Activity są przekazywane bezpośrednio na poziomie API platformy Azure za pomocą kilku kliknięć.

### 2. Integracje przez API (External Providers)
Wykorzystywane do łączenia się z chmurami firm trzecich (np. AWS CloudTrail, Salesforce). Sentinel łączy się bezpośrednio z API dostawcy usługi i cyklicznie pobiera logi.

### 3. Agenci i Bramy logów (Syslog / CEF)
Stosowane dla urządzeń lokalnych (On-premises), takich jak firewalle (Cisco, Fortinet, CheckPoint) oraz serwery Linux/Windows.
* Urządzenia wysyłają logi w formacie **Syslog** lub **CEF** (Common Event Format) do dedykowanego serwera zbierającego (Log Forwarder) znajdującego się w sieci lokalnej.
* Serwer ten posiada zainstalowanego agenta **Azure Monitor Agent (AMA)**, który szyfruje ruch i przesyła go do Log Analytics Workspace w Azure.

---

> [!CAUTION] Pułapka licencyjna na egzaminie:
> Logi z rodziny Microsoft 365 (w tym *Azure Activity Logs*, *Office 365 Audit Logs* — SharePoint, Exchange, Teams) są w większości przypadków **darmowe** do zaciągania do Sentinel. Jednak logi z zapór sieciowych (Syslog/CEF) czy logi logowań Entra ID generują koszty oparte na gigabajtach (GB) danych. Zawsze sprawdzaj wymagania dotyczące filtrowania logów u źródła.