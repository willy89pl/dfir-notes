---
tags:
  - sc-200
  - microsoft
  - identity
  - iam
  - security
  - governance
category: sc-200
---


# IAM (Identity and Access Management) — Charakterystyka (Pod SC-200)

**IAM (Zarządzanie Tożsamością i Dostępem)** to fundamentalna dyscyplina bezpieczeństwa IT oraz ramy architektoniczne (framework), które gwarantują, że **właściwe osoby (lub systemy)** mają dostęp do **właściwych zasobów**, z **właściwych powodów** i we **właściwym czasie**. 

W świecie chmury Microsoftu (Azure / M365) IAM to pierwsza i najważniejsza linia obrony, realizująca strategię **Zero Trust** (gdzie tożsamość staje się nowym obwodem bezpieczeństwa, zastępując tradycyjny firewall sieciowy).

---

## Cztery Filary IAM

Każdy proces IAM składa się z czterech powiązanych ze sobą etapów. Musisz je odróżniać na egzaminie:

1. **Identyfikacja (Identification):** Proces, w którym użytkownik mówi systemowi, kim jest (np. wpisuje swój login: `jan.kowalski@firma.pl`).
2. **Uwierzytelnianie (Authentication - AuthN):** Proces weryfikacji tożsamości. System sprawdza, czy użytkownik to naprawdę on (np. poprzez sprawdzenie hasła, tokenu farty, powiadomienia push MFA, czy biometrii).
3. **Autoryzacja (Authorization - AuthZ):** Proces sprawdzania, do czego uwierzytelniony użytkownik ma prawo (np. mechanizm **RBAC** sprawdza, czy Jan Kowalski ma uprawnienia do odczytu bazy danych).
4. **Rozliczalność / Audyt (Accountability / Auditing):** Śledzenie działań użytkownika. Rejestrowanie w logach (np. *Audit Logs*, *Sign-in Logs*), co dokładnie użytkownik robił po uzyskaniu dostępu.



---

## Perspektywa SC-200 (Kluczowe Koncepcje Bezpieczeństwa)

Jako analityk SecOps, większość incydentów w Microsoft Sentinel będzie powiązana z naruszeniem lub próbą obejścia zasad IAM. Na egzaminie skup się na tych obszarach:

### 1. Zasada Minimalnych Uprawnień (Principle of Least Privilege - PoLP)
Użytkownicy i aplikacje powinni mieć przypisane **tylko takie uprawnienia, które są im absolutnie niezbędne** do wykonania bieżącej pracy – i nic ponadto. 
* *Przykład:* Deweloper nie potrzebuje roli *Owner* (Właściciel) na stałe. Powinien mieć rolę *Contributor* (Współtwórca) lub uzyskiwać wyższe prawa czasowo przez mechanizm PIM (Privileged Identity Management).

### 2. Tożsamości Nietekstowe (Non-human Identities)
IAM to nie tylko ludzie. W chmurze Azure aplikacje i skrypty muszą bezpiecznie logować się do innych zasobów (np. aplikacja webowa musi pobrać hasło z Azure Key Vault). Pod SC-200 musisz znać dwa pojęcia:
* **Service Principals (Nazwy główne usług):** Tożsamość stworzona dla aplikacji w Entra ID (odpowiednik konta usługowego w lokalnym AD). Wymaga zarządzania sekretami/certyfikatami.
* **Managed Identities (Tożsamości zarządzane):** Bezpieczniejsza ewolucja Service Principal. Azure w pełni sam zarządza poświadczeniami tej tożsamości – programista nie widzi hasła i nie może go zgubić ani wyciec na GitHubie.

### 3. Przeglądy Dostępu (Access Reviews)
Element zarządzania ładem (Identity Governance). Automatyczny proces, który cyklicznie (np. co kwartał) wymusza na menedżerach lub właścicielach zasobów weryfikację, czy ich pracownicy nadal potrzebują swoich uprawnień (np. ról administracyjnych lub dostępu do wrażliwych grup w Teams). Jeśli menedżer nie potwierdzi zasadności, system automatycznie odbiera uprawnienia.

---

## Co monitorujesz w SOC (Wykrywanie ataków na IAM)?

Wszystkie zdarzenia IAM z Entra ID i Azure trafiają do **Log Analytics Workspace**, gdzie analizujesz je w **Microsoft Sentinel**:

* **Udana autoryzacja po wielu nieudanych próbach:** Klasyczny wzorzec ataku *Brute Force* lub *Password Spraying*, który ostatecznie się powiódł.
* **Manipulacja przy Service Principals:** Hakerzy, po przejęciu uprawnień admina, często tworzą własne Service Principals lub dodają nowe certyfikaty do istniejących aplikacji, aby zapewnić sobie stały tylny dostęp (Persistence) odporny na zmianę haseł zwykłych użytkowników.
* **Bypassowanie MFA:** Monitorowanie logów pod kątem rejestracji nowych urządzeń MFA z podejrzanych lokalizacji (ataki typu *MFA Session Hijacking*).

> [!TIP] Szybka powtórka przed egzaminem:
> * **AuthN (Uwierzytelnianie):** Ktoś udowadnia, kim jest (Zabezpieczane przez: *MFA*, *Entra ID Protection*).
> * **AuthZ (Autoryzacja):** Ktoś próbuje coś zrobić (Zabezpieczane przez: *Azure RBAC*, *Conditional Access*).