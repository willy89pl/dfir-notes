---
tags:
  - sc-200
  - microsoft
  - defender
  - endpoint
  - intune
category: sc-200
---

# Device Groups — Defender vs Intune (Różnice pod SC-200)

Pojęcie **Device Groups** (Grupy urządzeń) pojawia się zarówno w **Microsoft Defender for Endpoint (MDE)**, jak i w **Microsoft Intune**. Choć nazwa jest identyczna, oba systemy używają grup do zupełnie innych celów operacyjnych.

---

## 1. Device Groups w Microsoft Defender for Endpoint (Bezpieczeństwo)

W portalu Defender XDR grupy urządzeń tworzy się po to, aby zarządzać **dostępem dla analityków SOC** oraz kontrolować **poziom automatyzacji systemów obronnych**.

Grupy w Defenderze buduje się dynamicznie na podstawie reguł (np. nazwa domeny, system operacyjny, czy tag przypisany do maszyny).

### Do czego służą w Defenderze?
* **Remediation Level (Poziom automatyzacji):** Przypisujesz, czy dana grupa maszyn (np. serwery) ma mieć poziom automatyzacji *Semi* (wymaga zgody SOC na usunięcie pliku), czy *Full* (antywirus sam czyści wszystko).
* **RBAC (Uprawnienia SOC):** Możesz ograniczyć dostęp analitykom. Przykładowo: analitycy z filii w Warszawie widzą alerty i mogą izolować maszyny tylko z grupy urządzeń `RG-Warsaw-Endpoints`. Nie mają wglądu w komputery z filii w Londynie.
* **Priorytetyzacja:** Pozwalają na logiczne oddzielenie maszyn krytycznych (np. komputery kadry zarządzającej - VIP, serwery produkcyjne) od zwykłych stacji roboczych.

---

## 2. Device Groups w Microsoft Intune (Zarządzanie i Konfiguracja)

W Intune (który technicznie pod spodem wykorzystuje grupy bezpieczeństwa **Microsoft Entra ID**) grupy urządzeń służą do **wypychania konfiguracji, aplikacji i polityk zgodności**.

Grupy te mogą być statyczne (ręcznie dodajesz komputery) lub dynamiczne (np. automatycznie grupuj wszystkie urządzenia z systemem iOS).

### Do czego służą w Intune?
* **Wdrażanie polityk (Configuration Profiles):** Jeśli chcesz wymusić na laptopach blokadę USB lub konfigurację VPN, przypisujesz tę politykę do grupy urządzeń w Intune.
* **Instalacja oprogramowania:** Wskazujesz grupę maszyn, na których Intune ma automatycznie zainstalować np. przeglądarkę Chrome lub pakiet Office.
* **Polityki zgodności (Compliance Policies):** Przypisujesz reguły (np. "Wymagany BitLocker"), na podstawie których Intune ocenia, czy urządzenie jest bezpieczne.

---

## Podsumowanie i Matryca SC-200

Podczas analizy pytań egzaminacyjnych i incydentów w SOC stosuj prostą regułę:

| Celem operacji jest... | Właściwe narzędzie |
| :--- | :--- |
| Zmiana poziomu izolacji / czyszczenia malware na maszynie | **Device Groups w Microsoft Defender** |
| Ograniczenie analitykowi SOC widoczności maszyn z danego kraju | **Device Groups w Microsoft Defender** |
| Wypchnięcie nowej tapety firmowej lub konfiguracji Wi-Fi | **Device Groups w Microsoft Intune (Entra ID)** |
| Aktualizacja poprawek Windows Update na komputerach | **Device Groups w Microsoft Intune (Entra ID)** |

> [!TIP] Punkt wspólny:
> Za pomocą grup w Intune możesz wdrożyć na komputery politykę EDR, która... automatycznie zrobi onboarding maszyn do Defendera i przypisze im odpowiednie tagi, na podstawie których Defender wrzuci je do swoich grup urządzeń.