---
tags:
  - sc-200
  - microsoft
  - cloud
  - paas
category: sc-200
---
# PaaS (Platform as a Service) — W skrócie

**PaaS (Platforma jako usługa)** to model chmurowy przeznaczony głównie dla programistów i administratorów baz danych. Dostawca chmury dostarcza kompletne środowisko uruchomieniowe (system operacyjny, bazę danych, serwer WWW), a zadaniem klienta jest jedynie wgranie własnego kodu aplikacji lub danych.



### 🏢 Przykłady w Azure
* **Azure App Services** (hostowanie stron i API bez zarządzania systemem Windows/Linux)
* **Azure SQL Database** (gotowa baza danych bez konieczności instalacji i aktualizacji SQL Servera)
* **Azure Functions** (Serverless – uruchamianie czystego kodu na żądanie)

---

## 🛡️ Perspektywa SC-200 & Bezpieczeństwo

W modelu PaaS granica odpowiedzialności przesuwa się w stronę dostawcy:

* **Za co odpowiada Microsoft:** Za łatanie systemu operacyjnego (OS), sprzęt, sieć fizyczną, wirtualizację oraz bezpieczeństwo samej platformy (np. silnika bazy danych).
* **Za co odpowiadasz TY (Klient):** 1. **Kod aplikacji:** Musisz dbać o to, aby Twoja aplikacja nie miała podatności (np. na SQL Injection).
	2. **Konfiguracja sieciowa:** Odpowiadasz za to, czy usługa PaaS jest wystawiona do publicznego internetu, czy ukryta w prywatnej sieci (np. przez *Azure Private Endpoint*).
	3. **Tożsamość i uprawnienia:** Kto i z jakimi prawami (RBAC) może zarządzać tą platformą.

> [!IMPORTANT] Zapamiętaj pod egzamin
> Ataki na usługi PaaS często polegają na błędach w konfiguracji (Misconfiguration) lub lukach w kodzie aplikacji. W agendzie SC-200 kluczowym narzędziem do ochrony usług PaaS jest **Microsoft Defender for Cloud** (konkretnie moduły dedykowane dla baz danych SQL, Storage Accounts czy App Services), który wykrywa np. nietypowe zapytania do baz danych czy próby eksfiltracji danych.