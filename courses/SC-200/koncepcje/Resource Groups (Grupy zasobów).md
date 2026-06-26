---
tags:
  - sc-200
  - microsoft
  - azure
category: sc-200
---

# Azure Resource Groups (Grupy zasobów) — Perspektywa SC-200

**Resource Group (RG / Grupa zasobów)** to logiczny kontener na platformie Microsoft Azure, który służy do grupowania, organizowania i wspólnego zarządzania powiązanymi ze sobą zasobami chmurowymi (takimi jak maszyny wirtualne, sieci, bazy danych czy workspace'y). 

Zasada jest prosta: każdy zasób w Azure musi należeć do **dokładnie jednej** grupy zasobów.

---

## Gdzie Resource Group pojawia się na egzaminie SC-200?

Na egzaminie SC-200 nie będziesz pytany o ogólne tworzenie grup zasobów (to domena egzaminu AZ-104), ale musisz rozumieć ich rolę w trzech konkretnych scenariuszach bezpieczeństwa:

### 1. Granica Uprawnień (RBAC — Role-Based Access Control)
Grupa zasobów to najpopularniejszy poziom, na którym analitykom SOC nadaje się uprawnienia. 
* Zamiast dawać komuś uprawnienia do całej subskrypcji Azure, tworzy się dedykowaną grupę zasobów (np. `rg-cybersecurity-prod`), w której umieszcza się Microsoft Sentinel.
* Analitykowi SOC nadaje się rolę (np. *Microsoft Sentinel Responder*) **tylko na poziomie tej konkretnej grupy zasobów**. Dzięki temu ma on pełen dostęp do Sentinela, ale nie ma żadnego wglądu w maszyny produkcyjne czy bazy danych HR znajdujące się w innych grupach zasobów.

### 2. Architektura Wdrożenia Microsoft Sentinel
Podczas wdrażania Sentinela, system zapyta Cię, w jakiej subskrypcji i w jakiej **Resource Group** chcesz utworzyć Log Analytics Workspace. Z punktu widzenia bezpieczeństwa i organizacji laba/firmy:
* Wszystkie elementy powiązane z SOC (Sentinel, Log Analytics Workspace, powiązane konta Automation) powinny znajdować się w **jednej, dedykowanej grupie zasobów**, odizolowanej od zasobów biznesowych.

### 3. Uprawnienia dla Playbooków (Automatyzacji SOAR)
To absolutnie kluczowy punkt, na którym Microsoft często "łapie" zdających SC-200:
* Playbooki (oparte na Azure Logic Apps) to osobne zasoby, które również muszą leżeć w jakiejś grupie zasobów (często tej samej co Sentinel lub osobnej `rg-sentinel-playbooks`).
* Aby Sentinel mógł uruchomić Playbook w celu zablokowania użytkownika czy izolacji hosta, usłudze Sentinel trzeba jawnie nadać uprawnienia *Microsoft Sentinel Automation Contributor* **właśnie na poziomie grupy zasobów, w której te Playbooki się znajdują**.

---

## Cykl Życia Zasobów (Złota zasada laba)

Grupa zasobów współdzieli cykl życia (Lifecycle) swoich elementów. 
* Jeśli usuniesz całą grupę zasobów, Azure automatycznie usunie **wszystkie** zasoby znajdujące się w środku.

> [!TIP] Wskazówka do Twojego laba:
> Gdy skończysz testy i naukę do SC-200, możesz usunąć całą grupę zasobów powiązaną z Sentinel/MDE, aby natychmiast zatrzymać naliczanie opłat w Azure, bez konieczności kasowania każdego komponentu z osobna.