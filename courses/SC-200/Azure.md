---
tags:
  - active-directory
  - sc-200
  - microsoft
  - azure
  - iaas
  - paas
  - saas
category: sc-200
---

---
tags:
  - SC-200
  - Cloud
  - Azure
  - Microsoft
---
# Microsoft Azure — Charakterystyka

**Microsoft Azure** (często nazywany po prostu Azure) to publiczna platforma chmurowa (Cloud Computing) stworzona przez Microsoft. Oferuje ponad 200 usług w modelach **IaaS** (Infrastruktura jako usługa), **PaaS** (Platforma jako usługa) oraz **[[SaaS]]** (Oprogramowanie jako usługa), umożliwiając budowanie, testowanie, wdrażanie i zarządzanie aplikacjami poprzez globalną sieć centrów danych Microsoftu.
Sam Azure identyfikowane jest jako [[IaaS]]

---

## Podstawowa Architektura Azure (Struktura Zasobów)

Azure organizuje zasoby w ścisłą hierarchię, co ma kluczowe znaczenie dla zarządzania uprawnieniami i monitorowania bezpieczeństwa:

1. **Zasób (Resource):** Pojedyncza usługa, np. Maszyna wirtualna (VM), Baza danych SQL, czy konto dyskowe (Storage Account).
2. **Grupa Zasobów (Resource Group - RG):** Logiczny kontener, do którego wrzuca się powiązane zasoby (np. wszystkie elementy składające się na jedną aplikację WWW), aby łatwiej nimi zarządzać i usuwać je zbiorczo.
3. **Subskrypcja (Subscription):** Jednostka rozliczeniowa i granica dostępu. Każda subskrypcja jest powiązana z metodą płatności i generuje osobne faktury.
4. **Grupy Zarządzania (Management Groups):** Kontenery nad subskrypcjami. Pozwalają na masowe nakładanie polityk bezpieczeństwa (Azure Policy) i uprawnień na wiele subskrypcji jednocześnie.

---

## Perspektywa SC-200 (Cybersecurity / SecOps w Azure)

Na egzaminie SC-200 Azure nie jest omawiany pod kątem "jak postawić serwer", ale **"jak zabezpieczyć architekturę chmurową i wykrywać w niej zagrożenia"**. Jako analityk SOC musisz znać kluczowe filary bezpieczeństwa Azure:

### 1. Zarządzanie Dostępem (IAM / RBAC)
* W Azure dostępem steruje się za pomocą **RBAC (Role-Based Access Control)**. 
* Uprawnienia przypisuje się na konkretnym poziomie hierarchii (np. użytkownik ma rolę *Czytelnika* na poziomie Grupy Zarządzania, więc widzi wszystko we wszystkich subskrypcjach poniżej).

### 2. Microsoft Defender for Cloud (Główny temat SC-200!)
To jedno z najważniejszych narzędzi w agendzie egzaminu. Odpowiada za:
* **CSPM (Cloud Security Posture Management):** Ocenia stan bezpieczeństwa Twojego Azure (daje tzw. *Secure Score*), wykrywa podatności (np. otwarte porty SSH do internetu) i sprawdza zgodność z normami (ISO, NIST).
* **CWPP (Cloud Workload Protection Platform):** Aktywna ochrona serwerów, kontenerów (AKS), baz danych i pamięci masowych przed malwarem i atakami w czasie rzeczywistym.

### 3. Logowanie i Analiza (Podstawa pod Microsoft Sentinel)
Aby cokolwiek wykryć w chmurze, musisz zbierać logi. W Azure kluczowe są:
* **Azure Activity Logs:** Logi operacyjne – kto, kiedy i co zrobił w infrastrukturze Azure (np. "Kto usunął bazę danych?", "Kto stworzył nową maszynę wirtualną?").
* **Resource Logs (Diagnostic Logs):** Logi generowane wewnątrz konkretnych usług (np. zapytania do bazy danych, ruch przez Firewall).
* Te logi są strumieniowane do repozytorium **Log Analytics Workspace**, z którego korzysta **Microsoft Sentinel** do wykrywania incydentów.

---

## Model Współdzielonej Odpowiedzialności (Shared Responsibility Model)

Przechodząc do Azure, bezpieczeństwo dzielone jest między klienta a Microsoft:
* **W chmurze (SaaS/PaaS):** Microsoft dba o fizyczne serwery, kable i zasilanie.
* **Twoja działka:** Bez względu na model (IaaS, PaaS czy SaaS), Ty **ZAWSZE** odpowiadasz za bezpieczeństwo swoich danych, tożsamości pracowników (konta i hasła) oraz urządzeń końcowych.

> [!TIP] Szybkie powiązanie pojęć pod egzamin:
> * **Lokalna sieć (On-premise):** Chroniona przez *Microsoft Defender for Identity* (analiza AD).
> * **Chmura Azure (Infrastruktura):** Chroniona przez *Microsoft Defender for Cloud* oraz monitorowana przez *Microsoft Sentinel*.