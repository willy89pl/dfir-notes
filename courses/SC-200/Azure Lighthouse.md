---
tags:
  - active-directory
  - sc-200
  - sentinel
  - mutlitenant
  - lighthouse
  - architecture
category: sc-200
---

# Azure Lighthouse

**Azure Lighthouse** to technologia umożliwiająca **zarządzanie zasobami międzydzierżawowo (Cross-tenant management)** z zachowaniem wysokiego poziomu automatyzacji, skalowalności i pełnej audytowalności działań.

Używają jej głównie dostawcy usług zarządzanych (MSP/MSSP) oraz wielkie korporacje o strukturze wielospółkowej (holdingi posiadające osobne dzierżawy Microsoft Entra ID).

---

## Jak działa mechanizm delegacji?

Zamiast tworzenia kont gości (B2B Guest Users) w strukturach klienta, proces onboardingu w Lighthouse przypisuje konkretne role RBAC użytkownikom z dzierżawy zarządzającej (np. firmy SOC) do subskrypcji lub grup zasobów w dzierżawie klienta.

* **Bezpieczeństwo:** Klient w pełni kontroluje zakres delegacji i może odebrać dostęp w dowolnym momencie.
* **Audytowalność:** Każda akcja zewnętrznego inżyniera jest logowana bezpośrednio w dzienniku aktywności (Activity Log) należącym do klienta.

---

## Relacja z Microsoft Sentinel (Kluczowe pod SC-200)

Azure Lighthouse jest fundamentem dla **zcentralizowanego SOC (Centralized SOC Architecture)**. 

Dzięki niemu analitycy bezpieczeństwa w swojej własnej konsoli Sentinela mogą:
* **Widzieć incydenty z wielu firm naraz:** Zarządzać i triażować incydenty (Incidents) w jednym widoku dla wielu odrębnych instancji Microsoft Sentinel rozmieszczonych u różnych klientów.
* **Pisać zapytania KQL w poprzek dzierżaw:** Uruchamiać polowania na zagrożenia (Threat Hunting) przeszukujące logi wielu organizacji jednocześnie za pomocą jednej komendy.
* **Wdrażać reguły masowo:** Publikować reguły analityczne czy playbooki automatyzacji z jednego repozytorium do wszystkich zarządzanych środowisk klientów.