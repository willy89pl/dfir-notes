---
tags:
  - sc-200
  - microsoft
  - saas
  - cloud
category: sc-200
---
# SaaS (Software as a Service) — W skrócie

**SaaS (Oprogramowanie jako usługa)** to model chmurowy, w którym użytkownik otrzymuje gotową do użycia aplikację działającą w przeglądarce internetowej lub dedykowanym kliencie. Cała infrastruktura, kod aplikacji i serwery są w 100% zarządzane przez dostawcę chmury.



### Przykłady w ekosystemie Microsoft
* **Microsoft 365** (Outlook, Teams, SharePoint)
* **Microsoft Sentinel** (jako chmurowy SIEM)
* **Dynamics 365**

---

## Perspektywa SC-200 & Bezpieczeństwo

W modelu SaaS obowiązuje **Model Współdzielonej Odpowiedzialności (Shared Responsibility Model)**:

* **Za co odpowiada Microsoft:** Za fizyczne serwery, dostępność aplikacji, aktualizacje kodu, łatanie podatności systemu oraz bezpieczeństwo fizyczne centrum danych.
* **Za co odpowiadasz TY (Klient):** 1. **Tożsamość i dostęp (Identity & Access):** Kto ma konto, jak silne są hasła, czy włączone jest MFA (Multi-Factor Authentication).
	2. **Dane (Data):** Co użytkownicy wpisują do aplikacji i komu to udostępniają (ochrona przed wyciekiem danych).
	3. **Urządzenia (Devices):** Czy komputery logujące się do usługi nie są zainfekowane.

> [!IMPORTANT] Zapamiętaj pod egzamin
> Najczęstszym wektorem ataku na usługi SaaS nie jest "włamanie do serwerów Microsoftu", ale **przejęcie konta użytkownika** (Identity Theft) za pomocą phishingu lub wycieku poświadczeń. Głównym narzędziem ochrony SaaS w portfolio Microsoftu jest **Microsoft Defender for Office 365** oraz **Microsoft Entra ID ID Protection**.