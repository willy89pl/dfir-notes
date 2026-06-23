---
tags:
  - sc-200
  - microsoft
  - azure
  - entraid
  - cloud
  - paas
category: sc-200
---


# Microsoft Entra ID — Charakterystyka (Pod SC-200)

**Microsoft Entra ID** (dawniej Azure Active Directory lub Azure AD) to chmurowy system zarządzania tożsamością i dostępem (IAM – Identity and Access Management) od Microsoftu. Jest to **płaska struktura** (w przeciwieństwie do hierarchicznego lokalnego AD[[AD on-prem (and with cloud)]]) oparta na tzw. Lokatorach (Tenants), stworzona do obsługi nowoczesnych aplikacji internetowych, chmury Azure oraz Microsoft 365.
Działa jako [[PaaS]] - sam musisz nią administrować.

---

## Kluczowe Pojęcia i Funkcje

* **Tożsamość Hybrydowa:** Za pomocą narzędzia **Microsoft Entra Connect**, lokalne konta z Active Directory (AD DS) są synchronizowane do chmury Entra ID. Hakerzy często próbują wykorzystać tę relację do poruszania się między środowiskami.
* **Nowoczesne Protokoły:** Entra ID **NIE UŻYWA** Kerberosa ani NTLM. Działa w oparciu o protokoły webowe: **OAuth 2.0, SAML 2.0, OpenID Connect (OIDC)** oraz Graph API.
* **Wieloskładnikowe Uwierzytelnianie (MFA):** Podstawa strategii Zero Trust. Entra ID pozwala na wymuszenie MFA za pomocą aplikacji Microsoft Authenticator, kluczy FIDO2 czy SMS-ów.

---

## Najważniejsze Tematy SC-200 (Mechanizmy Ochrony)

Na egzaminie SC-200 musisz doskonale rozumieć dwa zaawansowane mechanizmy bezpieczeństwa wbudowane w Entra ID:

### 1. Dostęp Warunkowy (Conditional Access - CA)
To "mózg" bezpieczeństwa Microsoftu realizujący zasadę *Zawsze weryfikuj* (Zero Trust). Działa na zasadzie instrukcji warunkowych: **JEŚLI [Sygnał], TO [Wymuś akcję]**.



* **Sygnały (Wejście):** Kto się loguje (użytkownik/grupa), z jakiej lokalizacji (IP/kraj), z jakiego urządzenia (czy jest bezpieczne/zarządzane przez Intune) oraz jaka aplikacja jest otwierana.
* **Wymuszenia (Decyzja):** * Zablokuj dostęp.
	* Zezwól na dostęp (czyste logowanie).
	* Wymuś MFA (najczęstszy scenariusz).
	* Wymuś zmianę hasła.

### 2. Microsoft Entra ID Protection (Dawniej Azure AD Identity Protection)
Moduł wykorzystujący uczenie maszynowe Microsoftu do analizy miliardów logowań dziennie w celu wykrywania anomalii. Generuje dwa rodzaje ryzyka:
* **User Risk (Ryzyko Użytkownika):** Prawdopodobieństwo, że poświadczenia użytkownika wyciekły (np. znaleziono jego hasło w darknecie).
* **Sign-in Risk (Ryzyko Logowania):** Prawdopodobieństwo, że dane logowanie nie jest wykonywane przez właściciela konta (np. logowanie z nietypowej lokalizacji, nowej przeglądarki lub tzw. *Impossible Travel* – logowanie z Polski i z USA w odstępie 10 minut).

Te ryzyka mogą być użyte jako **Sygnały** w opisanych wyżej polisach Dostępu Warunkowego (np. *Jeśli Sign-in Risk = Medium, wymuś MFA*).

---

## Monitorowanie w SOC (Microsoft Sentinel)

Jako analityk SecOps, Entra ID dostarcza Ci najważniejszych logów do wykrywania ataków (np. *Password Spraying*, *Brute Force*, *MFA Fatigue*):

* **Sign-in Logs:** Informacje o każdym logowaniu (kto, skąd, czy się udało, dlaczego CA zablokowało dostęp).
* **Audit Logs:** Logi administracyjne – kto stworzył użytkownika, kto zmienił uprawnienia, kto wyłączył MFA dla danego konta.
* **Non-interactive Sign-in Logs:** Logi generowane automatycznie przez aplikacje w tle (często tam hakerzy próbują ukryć swoją aktywność).

> [!CAUTION] Krytyczny punkt egzaminacyjny
> Atak typu **MFA Fatigue (MFA Spamming)** polega na wielokrotnym wysyłaniu powiadomień push na telefon ofiary, aż ta dla świętego spokoju kliknie "Zatwierdź". W Entra ID broni się przed tym za pomocą funkcji **Number Matching** (użytkownik musi przepisać numer wyświetlany na ekranie komputera do aplikacji w telefonie).