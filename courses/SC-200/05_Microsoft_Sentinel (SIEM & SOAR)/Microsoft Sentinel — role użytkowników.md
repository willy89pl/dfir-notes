---
tags:
  - sc-200
  - microsoft
  - sentinel
  - rbac
category: sc-200
---
# Microsoft Sentinel — Role i Uprawnienia (RBAC)

Zarządzanie dostępem do systemu Microsoft Sentinel opiera się na modelu **RBAC (Role-Based Access Control)** na platformie Azure. Uprawnienia te przypisuje się zazwyczaj na poziomie **Resource Group (Grupy zasobów)**, w której znajduje się Log Analytics Workspace powiązany z Sentienelem.

Wyróżniamy trzy główne, dedykowane role dla Sentinela oraz kilka ról wspierających, które musisz znać na egzaminie SC-200.

---

## Trzy Główne Role Microsoft Sentinel

Każda kolejna rola zawiera w sobie wszystkie uprawnienia roli niższej.

| Nazwa roli | Poziom (TIER) w SOC | Co może robić? | Czego NIE może robić? |
| :--- | :--- | :--- | :--- |
| **Microsoft Sentinel Reader**<br>*(Czytelnik)* | **Tier 1 (Stażysta / Audytor)** | Przeglądać incydenty, oglądać dashboardy (Workbooks), czytać logi za pomocą KQL, przeglądać reguły analityczne. | Nie może wprowadzać żadnych zmian: przypisywać incydentów do siebie, zmieniać ich statusów, edytować reguł ani zamykać zgłoszeń. |
| **Microsoft Sentinel Responder**<br>*(Reagujący)* | **Tier 2 (Analityk SOC)** | Wszystko to co Reader, plus: pełne zarządzanie incydentami (zmiana statusu z New na Active/Closed, przypisywanie właściciela, dodawanie komentarzy, ręczne uruchamianie Playbooków). | Nie może tworzyć ani edytować reguł analitycznych (Analytics Rules), zmieniać konfiguracji Sentinela ani edytować Workbooks. |
| **Microsoft Sentinel Contributor**<br>*(Współtwórca)* | **Tier 3 / SOC Lead<br>(Inżynier Security)** | Wszystko to co Responder, plus: tworzenie i edycja reguł analitycznych (KQL), instalowanie łączników danych (Data Connectors), tworzenie Workbooks, zarządzanie automatyzacją. | Nie może usuwać Log Analytics Workspace ani nadawać uprawnień innym użytkownikom (do tego potrzebna jest rola *Owner* subskrypcji). |

---

## Krytyczne Role Wspierające (Pułapki Egzaminacyjne)

Sam Microsoft Sentinel Contributor to za mało w zaawansowanych scenariuszach SOAR i inżynierii danych. Zapamiętaj te trzy role specjalne:

### 1. Microsoft Sentinel Automation Contributor
* **Do czego służy:** Pozwala usłudze Sentinel na fizyczne uruchamianie automatyzacji.
* **Zastosowanie pod egzamin:** Przypisuje się ją **samej usłudze Sentinel** (Managed Identity) na poziomie grupy zasobów, w której znajdują się Playbooki (Logic Apps). Bez tej roli, automatyzacja wyrzuci błąd i nie wykona akcji naprawczej.

### 2. Logic App Contributor / Operator
* **Do czego służy:** Uprawnienia do tworzenia i zarządzania procesami Logic Apps.
* **Zastosowanie pod egzamin:** Jeśli inżynier SOC ma za zadanie **tworzyć nowe Playbooki**, sama rola *Microsoft Sentinel Contributor* mu nie wystarczy. Musi dodatkowo otrzymać rolę **Logic App Contributor** na grupie zasobów, gdzie te playbooki będą zapisywane.

### 3. Log Analytics Contributor
* **Do czego służy:** Zarządzanie głęboką konfiguracją bazy danych (Log Analytics Workspace).
* **Zastosowanie pod egzamin:** Potrzebna inżynierowi SOC, jeśli musi on zmieniać retencję danych (czas przechowywania logów), konfigurować tabele niestandardowe (Custom Logs) lub zarządzać uprawnieniami na poziomie konkretnych tabel w bazie.

---

## Szybka Matryca Decyzyjna dla SOC (Pod Egzamin)

Gdy w pytaniu egzaminacyjnym widzisz zadanie, wybierz najniższą wymaganą rolę:

* *„Użytkownik musi tylko analizować wykresy w Workbooks i szukać logów w KQL”* ➔ **Sentinel Reader**
* *„Analityk musi zmieniać statusy incydentów i przypisywać je do zespołu”* ➔ **Sentinel Responder**
* *„Inżynier musi napisać nowe zapytanie KQL wykrywające techniki MITRE i wdrożyć je jako Alert”* ➔ **Sentinel Contributor**
* *„Należy umożliwić Sentinelowi wywoływanie Playbooków przy nowych incydentach”* ➔ **Sentinel Automation Contributor** (dla tożsamości Sentinela)