---
tags:
  - sc-200
  - microsoft
  - defender
  - cloudsecurity
  - mulitcloud
category: sc-200
---

# Microsoft Defender for Cloud (MDC)

**Microsoft Defender for Cloud** to platforma bezpieczeństwa typu **CNAPP** (Cloud-Native Application Protection Platform). Służy do centralnego zarządzania poziomem bezpieczeństwa (posture management) oraz aktywnej ochrony zasobów infrastrukturalnych (serwerów, baz danych, kontenerów) działających w chmurach i środowiskach hybrydowych.

Jest to rozwiązanie **wielochmurowe (Multi-cloud)**, co oznacza, że natywnie wspiera i chroni:
* Microsoft Azure
* Amazon Web Services (AWS)
* Google Cloud Platform (GCP)
* Środowiska lokalne (On-premises) za pośrednictwem usługi *Azure Arc*.

---

## Dwa Główne Filary MDC (Krytyczne na egzamin)



### 1. CSPM (Cloud Security Posture Management) — Zarządzanie Stanem Bezpieczeństwa
Filar defensywny i prewencyjny (Darmowy w podstawowej wersji dla Azure).
* **Secure Score:** Wylicza ogólny procentowy wskaźnik bezpieczeństwa dla wszystkich podpiętych chmur (Azure, AWS, GCP).
* **Rekomendacje:** Generuje listę zadań naprawczych (np. "Włącz MFA dla kont administracyjnych w AWS", "Zablokuj publiczny dostęp do dysku w Azure").
* **Regulatory Compliance:** Pozwala mapować infrastrukturę pod kątem spełniania norm prawnych (np. ISO 27001, PCI-DSS, NIST).

### 2. CWPP (Cloud Workload Protection Platform) — Aktywna Ochrona Zasobów
Filar ofensywny, wykrywający ataki w czasie rzeczywistym (Wymaga płatnych planów — *Defender Plans*). Generuje alerty bezpieczeństwa wysyłane bezpośrednio do Sentinela.

Plany ochrony (Defender Plans) obejmują m.in.:
* **Defender for Servers:** Chroni systemy operacyjne Windows/Linux (w Azure, AWS, GCP oraz on-prem). Automatycznie wdraża na nich agenta *Microsoft Defender for Endpoint (MDE)*.
* **Defender for Containers:** Służy do skanowania obrazów kontenerów pod kątem podatności oraz ochrony klastrów Kubernetes w czasie rzeczywistym (AKS w Azure, EKS w AWS, GKE w GCP).
* **Defender for Storage / SQL / Key Vault:** Wykrywa anomalie w dostępie do danych (np. próba masowego pobrania plików z konta dyskowego lub nietypowe zapytania SQL sugerujące wstrzyknięcie kodu).

---

## Słowa kluczowe pod scenariusze egzaminacyjne SC-200

Jeśli w pytaniu pojawią się poniższe hasła, Twoim celem wybór wariantu związanego z **Defender for Cloud**:
* **Multi-cloud / AWS / GCP / Azure Arc** ➔ Centralna ochrona infrastruktury poza Azure.
* **Secure Score / Recommendations** ➔ Podnoszenie poziomu odporności chmury na ataki (CSPM).
* **Skanowanie podatności maszyn lub kontenerów (Vulnerability Assessment)** ➔ Wykrywanie luk w oprogramowaniu na serwerach chmurowych.
* **Regulatory Compliance dashboards** ➔ Raportowanie zgodności z normami bezpieczeństwa (np. SOC 2).