---
tags:
  - sc-200
  - microsoft
  - defender
  - casb
category: sc-200
---
# Microsoft Defender for Cloud vs Defender for Cloud Apps

Mimo łudząco podobnych nazw, **Microsoft Defender for Cloud** oraz **Microsoft Defender for Cloud Apps** to dwa zupełnie różne systemy bezpieczeństwa. Różnica polega na tym, **co** te systemy chronią: jeden chroni infrastrukturę (IaaS/PaaS), a drugi aplikacje i dane użytkowników (SaaS).



---

## 1. Microsoft Defender for Cloud Apps (MDCA)
* **Klasa systemu:** CASB (Cloud Access Security Broker)
* **Co chroni?** Warstwę **SaaS** (Software as a Service) oraz aktywność użytkowników końcowych.
* **Cel:** Monitoruje, co Twoi pracownicy robią w aplikacjach chmurowych (M365, Salesforce, Dropbox, czaty AI). Wykrywa Shadow IT (nieautoryzowane aplikacje), blokuje wyciek danych (np. pobieranie dokumentów na prywatny komputer) i analizuje uprawnienia aplikacji OAuth.
* **W skrócie:** Chroni organizację przed błędami i celowym działaniem **użytkowników** w chmurach SaaS.

## 2. Microsoft Defender for Cloud (MDC)
* **Klasa systemu:** CNAPP (Cloud Native Application Protection Platform) — łączy w sobie funkcje CWPP (ochrona maszyn) oraz CSPM (zarządzanie posturą bezpieczeństwa).
* **Co chroni?** Warstwę **IaaS** (Infrastruktura) oraz **PaaS** (Platforma) w środowiskach wielochmurowych (**Azure, AWS, Google Cloud Platform — GCP**).
* **Cel:** Chroni serwery, maszyny wirtualne (VM), bazy danych (SQL), kontenery (Kubernetes) oraz sieci chmurowe przed atakami hakerskimi. Skanuje infrastrukturę pod kątem podatności i błędnych konfiguracji (np. otwarty port SSH na świat).
* **W skrócie:** Chroni Twoją **własną infrastrukturę i serwery** postawione w chmurze (Azure/AWS/GCP) przed zewnętrznymi cyberatakami.

---

## Porównanie dla analityka SOC (Matryca SC-200)

Aby nie pomylić tych pojęć na egzaminie, zapamiętaj poniższe scenariusze:

| Scenariusz / Problem w firmie | Którego systemu użyjesz? |
| :--- | :--- |
| Pracownik udostępnił poufny plik z firmowego OneDrive dla losowej osoby w Internecie. | **Microsoft Defender for Cloud Apps (MDCA)** |
| Pracownicy masowo zaczęli logować się do nieautoryzowanego, darmowego programu do edycji PDF w chmurze. | **Microsoft Defender for Cloud Apps (MDCA)** |
| Ktoś próbuje wykonać atak Brute-Force na Twoją maszynę wirtualną (Linux VM) działającą w AWS lub Azure. | **Microsoft Defender for Cloud (MDC)** |
| Twoja baza danych Azure SQL ma wyłączone szyfrowanie i jest podatna na wyciek. | **Microsoft Defender for Cloud (MDC)** |

---

> [!TIP] Pozycjonowanie w konsoli
> Zauważ to podczas pracy w labie: **MDCA** jest w pełni zintegrowany z portalem **Microsoft Defender XDR** (`security.microsoft.com`), ponieważ dotyczy bezpieczeństwa tożsamości i danych użytkowników. 
> 
> Z kolei **Microsoft Defender for Cloud (MDC)** posiada swoją osobną, dedykowaną konsolę bezpośrednio w portalu **Azure Portal** (`portal.azure.com`), ponieważ zarządzają nim głównie administratorzy chmury i inżynierowie DevSecOps odpowiedzialni za infrastrukturę serwerową.