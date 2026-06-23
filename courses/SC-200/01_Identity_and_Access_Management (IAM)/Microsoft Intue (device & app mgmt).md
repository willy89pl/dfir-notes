---
tags:
  - sc-200
  - microsoft
  - cloud
  - saas
  - endpoint
  - intune
category: sc-200
---

# Microsoft Intune — Charakterystyka (Pod SC-200)

**Microsoft Intune** to chmurowa usługa (model **SaaS**) służąca do zarządzania urządzeniami mobilnymi (**MDM** – Enterprise Mobility Management) oraz zarządzania aplikacjami mobilnymi (**MAM** – Mobile Application Management). Pozwala administratorom kontrolować, w jaki sposób urządzenia firmowe (oraz prywatne pracowników) są używane oraz jak chronione są na nich dane firmowe.

Obsługuje systemy: Windows, macOS, Linux, iOS/iPadOS oraz Android.

Warto dodać że pełni trochę takie funkcje jak GPO, można z niego zarządzać endpointami.

---

## Dwa Filary Działania Intune

### 1. MDM (Mobile Device Management) — Zarządzanie Urządzeniem
Urządzenie jest w pełni rejestrowane w firmie (Enrolled). Administrator ma nad nim pełną kontrolę.
* **Polityki Zgodności (Compliance Policies):** Definiowanie reguł, jakie urządzenie musi spełnić, aby uznać je za bezpieczne (np. włączony BitLocker, aktywny antywirus, minimalna wersja systemu operacyjnego, brak Jailbreaka/Roota).
* **Polityki Konfiguracji (Configuration Profiles):** Zdalne konfigurowanie ustawień (np. automatyczne ustawienie firmowego Wi-Fi, wymuszenie tapety, zablokowanie dostępu do Panelu Sterowania, konfiguracja VPN).

### 2. MAM (Mobile Application Management) — Zarządzanie Aplikacjami
Stosowane często w modelu **BYOD** (Bring Your Own Device – prywatny telefon pracownika). Firma nie kontroluje całego telefonu, a jedynie "kontener" z aplikacjami firmowymi (np. Outlook, Teams).
* **Polityki Ochrony Aplikacji (App Protection Policies):** Blokowanie możliwości kopiowania danych firmowych (np. zakaz kopiowania tekstu z maila w Outlooku firmowym i wklejania go do prywatnego Messengera lub WhatsAppa). Wymuszenie osobnego PIN-u do otwarcia Outlooka.

---

## Perspektywa SC-200 (Integracja z Security & Zero Trust)

Na egzaminie SC-200 Intune interesuje Cię głównie jako **narzędzie dostarczające sygnałów o stanie zdrowia urządzeń** do innych systemów bezpieczeństwa.

### 1. Intune + Dostęp Warunkowy (Conditional Access)
To absolutnie najważniejsze powiązanie. Intune sprawdza, czy urządzenie spełnia *Polityki Zgodności*. Jeśli komputer ma np. wyłączonego antywirusa, Intune oznacza go jako **Non-compliant** (Niezgodny). 
Wtedy Dostęp Warunkowy (Entra ID) otrzymuje ten sygnał i mówi: *"Próbujesz zalogować się do M365 z zainfekowanego komputera? Blokuję dostęp, dopóki go nie naprawisz"*.

### 2. Intune + Microsoft Defender for Endpoint (MDE)
Intune służy jako główne narzędzie do masowego wdrażania (Onboarding) antywirusa Defender na tysiące komputerów w firmie. 
Dodatkowo, dzięki integracji, jeśli Defender for Endpoint wykryje na komputerze aktywne złośliwe oprogramowanie (Malware), zgłasza to do Intune, który natychmiast zmienia status urządzenia na *Non-compliant*, automatycznie odcinając je od zasobów firmy przez Dostęp Warunkowy.

---

## Co monitorujesz w SOC (Logi z Intune)?

Jako analityk SecOps, logi z Intune przesyłasz do **Microsoft Sentinel** lub analizujesz w portalu bezpieczeństwa pod kątem anomalii:
* **Audit Logs:** Kto zmienił politykę bezpieczeństwa dla urządzeń? Kto wyłączył wymóg szyfrowania dysków?
* **Operational Logs:** Które urządzenia nagle przestały być zgodne z polityką firmy (potencjalne masowe infekcje).
* **Device Enrollment Logs:** Próby zarejestrowania podejrzanych, nieznanych urządzeń w sieci firmowej.

> [!IMPORTANT] Zapamiętaj pod egzamin SC-200
> * **Intune** odpowiada za *konfigurację i zgodność* urządzenia (Compliance & Configuration).
> * **Defender for Endpoint (MDE)** odpowiada za *wykrywanie i reagowanie na aktywne ataki* (Detection & Response).
> * Wspólnie realizują architekturę **Microsoft XDR**, ściśle współpracując z Dostępem Warunkowym.
