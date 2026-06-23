---
tags:
  - sc-200
  - microsoft
  - defender
  - xdr
  - security
category: sc-200
---

# Microsoft Defender Ecosystem — Wielka Ściąga (Pod SC-200)

W starych dokumentach można spotkać nazwę ATP (Advanced Threat Protection)

Microsoft dzieli swoje portfolio obronne na dwie główne gałęzie pod parasolem **Microsoft Defender XDR**: 
* ochronę środowiska pracy (M365)
* ochronę chmury i infrastruktury (Azure/Multi-cloud). 
Ich głównym celem jest automatyczna korelacja alertów z różnych warstw w spójne **Incydenty**.

---

## 1. Microsoft Defender XDR (Środowisko M365 & Tożsamość)
Zestaw narzędzi chroniący użytkowników, ich urządzenia, pocztę oraz aplikacje SaaS.

| Nazwa produktu                     | Co dokładnie chroni?                                                                                                            | Kluczowa funkcja pod SC-200                                                                                                                     |
| :--------------------------------- | :------------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Defender for Endpoint (MDE)**    | Urządzenia końcowe (Laptopy, telefony, serwery Windows/Linux).                                                                  | Klasyczny EDR. Wykrywa anomalie w procesach, izoluje zainfekowane komputery od sieci.                                                           |
| **Defender for Office 365 (MDO)**  | Poczta (Exchange Online), Teams, SharePoint, OneDrive.                                                                          | Funkcje *Safe Links* (skanowanie URL w czasie rzeczywistym) oraz *Safe Attachments* (sandbox dla załączników). EOP (Exchange Online Protection) |
| **Defender for Identity (MDI)**    | Lokalne Active Directory ([[AD on-prem (and with cloud)]]) oraz serwery AD FS / Entra Connect.                                  | Analizuje ruch sieciowy z kontrolerów domeny. Wykrywa ataki takie jak *Kerberoasting*, *DCSync*, *Pass-the-Hash*.                               |
| **Defender for Cloud Apps (MDCA)** | Ruch do aplikacji chmurowych ([[CASB (Cloud Access Security Broker)]]) – zarówno MS, jak i firm trzecich (np. AWS, Salesforce). | Wykrywa Shadow IT (nieautoryzowane aplikacje), masowe pobieranie danych lub logowania z podejrzanych IP.                                        |
Dodatkowo (istotne moduły/funkcje/zdolności):

| Nazwa produktu                        | Co dokładnie chroni?                                                                               | Kluczowa funkcja pod SC-200                                                                                                                                                                                  |
| :------------------------------------ | :------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Defender Vulnerability Management** | Warstwa podatności i konfiguracji na urządzeniach końcowych (wbudowany w MDE lub jako standalone). | **Zarządzanie podatnościami (Proactive Security).** Ciągła inwentaryzacja softu, wykrywanie dziur (CVE) i błędów konfiguracji systemu w czasie rzeczywistym.                                                 |
| **Microsoft Entra ID Protection**     | Tożsamość chmurową i hybrydową użytkowników bezpośrednio w Microsoft Entra ID.                     | **Ocena ryzyka w czasie rzeczywistym.** Wykrywa próby przejęcia kont, niemożliwe podróże (*Impossible Travel*) i wycieki haseł do darknetu. Zasilacz sygnałów dla Dostępu Warunkowego (*User/Sign-in Risk*). |
| Microsoft Data Loss Prevention        |                                                                                                    |                                                                                                                                                                                                              |
| App Governance                        |                                                                                                    |                                                                                                                                                                                                              |



## 2. Microsoft Defender for Cloud (Infrastruktura & Chmura)
Osobny panel zarządzania bezpieczeństwem dedykowany dla zasobów chmurowych (Azure, AWS, GCP) oraz maszyn hybrydowych. Łączy dwie role: 
 * ***CSPM** (zarządzanie stanem bezpieczeństwa)
 * **CWPP** (aktywna ochrona zasobów).

### Kluczowe plany ochrony (Warto znać pod egzamin):
* **Defender for Servers:** Dodaje zaawansowaną ochronę (w tym agenta MDE) do maszyn wirtualnych chmurowych i lokalnych. Oferuje funkcję *JIT (Just-In-Time) VM Access* (otwieranie portów RDP/SSH tylko na określony czas dla konkretnego admina).
* **Defender for Storage:** Wykrywa próby wgrania malware na konta dyskowe (Storage Accounts) oraz nietypowe próby masowej eksfiltracji danych.
* **Defender for SQL / Containers / Key Vault:** Dedykowane warstwy ochrony dla baz danych, klastrów Kubernetes (AKS) oraz cyfrowych sejfów na klucze i hasła.

---

## Integracja w SOC: Defender vs Microsoft Sentinel

Częsta pułapka na egzaminie SC-200 to pomylenie ról Defendera i Sentinela. Zapamiętaj ten podział: 
1. **Microsoft Defender (XDR):** Działa lokalnie i kontekstowo. Wie wszystko o ekosystemie Microsoftu. Samodzielnie potrafi zablokować proces, usunąć maila czy cofnąć uprawnienia użytkownikowi w ułamku sekundy. 
2. **Microsoft Sentinel (SIEM/SOAR):** To "wielki mózg" na samym szczycie. Zbiera informacje z Defenderów, ale też z urządzeń sieciowych (Cisco, Fortinet), systemów SAP, chmury AWS i serwerów Linux. Służy do korelacji danych z całej firmy i zaawansowanego polowania na zagrożenia (**Threat Hunting**) za pomocą języka **KQL**. 
   
> [!TIP] Złota zasada SC-200
> Defender odpowiada za **wykrywanie i automatyczną akcję wewnątrz swoich domen** (poczta, komputer, tożsamość). Sentinel odpowiada za **widoczność całej firmy (Single Pane of Glass)** i zaawansowaną orkiestrację procedur bezpieczeństwa (Playbooks).