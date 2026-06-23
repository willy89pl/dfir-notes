---
tags:
  - sc-200
  - microsoft
  - defender
  - cloud
  - casb
category: sc-200
---

# Microsoft Defender for Cloud Apps (MDCA)

Microsoft Defender for Cloud Apps (MDCA) to zaawansowany broker bezpieczeństwa dostępu do chmury (Cloud Access Security Broker — **CASB**). Działa jako punkt kontrolny i strażnik ruchu między użytkownikami końcowymi a wszystkimi aplikacjami chmurowymi (SaaS) używanymi w organizacji — zarówno zatwierdzonymi przez IT (np. M365, Salesforce), jak i nieautoryzowanymi.

---

## Trzy Główne Zastosowania (Use Cases pod SC-200)

### 1. Wykrywanie Shadow IT (Cloud Discovery)
MDCA analizuje logi sieciowe z firewalli brzegowych, serwerów proxy lub bezpośrednio z agentów *Defender for Endpoint (MDE)*. Mapuje te dane ze swoim globalnym katalogiem ponad 31 000 aplikacji chmurowych, dając administratorom pełny wgląd w to, jakich nieoficjalnych narzędzi (np. prywatne dyski chmurowe, zewnętrzne czaty AI) używają pracownicy.

### 2. Kontrola w czasie rzeczywistym (Conditional Access App Control)
Dzięki integracji z Dostępem Warunkowym Entra ID, MDCA może działać jako **Reverse Proxy**. Zamiast blokować dostęp do aplikacji, pozwala użytkownikowi wejść, ale nakłada ograniczenia w czasie rzeczywistym na podstawie kontekstu sesji:
* Blokowanie pobierania poufnych plików na prywatne komputery niezarządzane przez Intune.
* Blokowanie możliwości wgrywania plików zawierających złośliwy kod do firmowych repozytoriów chmurowych.

### 3. Ochrona przed nadużyciami OAuth (App Governance)
Monitoruje uprawnienia i zachowanie aplikacji firm trzecich (App Registrations), którym użytkownicy nadali dostęp do swoich kont M365 (np. wtyczki kalendarza żądające pełnego dostępu do skrzynki mailowej).

## Identyfikacja Zdarzeń w KQL (Sentinel)

Główną tabelą gromadzącą wszystkie zdarzenia z aplikacji chmurowych w Microsoft Sentinel jest **`CloudAppEvents`**.

```kusto
// Przykład wyszukiwania masowego pobierania danych przez jednego użytkownika w krótkim czasie
CloudAppEvents
| where ActionType == "FileDownloaded"
| summarize Count = count() by UserPrincipalName, AccountObjectId, bin(TimeGenerated, 10m)
| where Count > 50