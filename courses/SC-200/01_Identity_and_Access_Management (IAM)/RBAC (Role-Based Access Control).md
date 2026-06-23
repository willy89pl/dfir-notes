---
tags:
  - sc-200
  - microsoft
  - identity
  - iam
  - azure
  - entraid
category: sc-200
---

# RBAC (Role-Based Access Control) — Charakterystyka (Pod SC-200)

**RBAC (Kontrola dostępu oparta na rolach)** to system zarządzania uprawnieniami stosowany przez Microsoft do autoryzacji użytkowników, grup i aplikacji. Zamiast przypisywać poszczególne uprawnienia (np. "pozwól usunąć dysk") bezpośrednio do konkretnej osoby, uprawnienia te są grupowane w **Role** (np. *Virtual Machine Contributor*), a te role przypisuje się użytkownikom na określonym poziomie hierarchii (**Scope**).

---

## Trzy Elementy Przypisania Roli (Role Assignment)

Zrozumienie modelu RBAC opiera się na prostym równaniu: **KTO + CO MOŻE ROBIĆ + GDZIE**.

1. **Security Principal (Kto?):** Użytkownik, grupa, usługa systemowa (Service Principal) lub tożsamość zarządzana (Managed Identity), której nadajemy uprawnienia.
2. **Role Definition (Co może robić?):** Zbiór uprawnień zapisany w formacie JSON (np. lista dozwolonych akcji `Actions` i zabronionych `NotActions`).
3. **Scope (Gdzie?):** Poziom (zakres) w strukturze Azure, na którym te uprawnienia mają obowiązywać. Uprawnienia są **dziedziczone** w dół hierarchii.

### Hierarchia dziedziczenia uprawnień w Azure (Scope):

*  **Grupa Zarządzania (Management Group)** — Najwyższy poziom
*  **Subskrypcja (Subscription)**
*  **Grupa Zasobów (Resource Group)**
*  **Zasób (Resource)** — Najniższy poziom (np. jedna maszyna VM)

*Przykład:* Jeśli nadasz użytkownikowi rolę *Właściciela* (Owner) na poziomie Subskrypcji, automatycznie będzie on Właścicielem każdej Grupy Zasobów i każdego pojedynczego zasobu w tej subskrypcji.

---

## Krytyczny Punkt Egzaminacyjny: Azure RBAC vs Entra ID Roles

To jedna z największych pułapek na egzaminie SC-200. Musisz bezbłędnie rozróżniać te dwa systemy ról, ponieważ zarządzają one zupełnie innymi zasobami!

| Cecha | Azure RBAC | Entra ID Roles (Dawniej Azure AD Roles) |
| :--- | :--- | :--- |
| **Co zabezpiecza?** | **Infrastrukturę Azure** (Maszyny wirtualne, sieci, bazy SQL, konta dyskowe, Microsoft Sentinel). | **Tożsamość i konfigurację SaaS** (Konta użytkowników, aplikacje enterprise, domeny, MFA, Conditional Access). |
| **Przykładowe role** | *Owner*, *Contributor*, *Reader*, *Log Analytics Contributor*. | *Global Administrator*, *User Administrator*, *Security Administrator*. |
| **Gdzie zarządzasz?** | Zakładka **IAM (Access Control)** przy konkretnym zasobie w Azure. | Portal **Microsoft Entra** -> Roles & admins. |

> [!WARNING] Zapamiętaj na egzamin
> **Global Administrator** w Entra ID domyślnie **NIE MA** dostępu do zasobów w subskrypcjach Azure (nie może np. usunąć maszyny wirtualnej deweloperów). Może jednak włączyć specjalny przełącznik w ustawieniach (*Access management for Azure resources*), który tymczasowo nada mu rolę *User Access Administrator* na poziomie roota całej infrastruktury Azure.

---

## 🛡️ Perspektywa SC-200 (Zasada Least Privilege & PIM)

Jako analityk SOC badasz incydenty związane z nadmiarowymi uprawnieniami (Privilege Escalation). W kontekście SC-200 kluczowe są dwa pojęcia:

### 1. Wbudowane role monitorowania (Koniecznie zapamiętaj do Sentinel/Defender):
* **Microsoft Sentinel Contributor:** Może pisać reguły analityczne, tworzyć dashboardy i zarządzać incydentami (pełne prawo do pracy w SOC).
* **Microsoft Sentinel Responder:** Może zarządzać incydentami (zmieniać status, przypisywać ludzi), ale nie może edytować reguł ani kodu zapytań.
* **Security Administrator (Entra ID):** Może zarządzać politykami bezpieczeństwa (np. Dostępem Warunkowym) i resetować hasła (oprócz haseł innych adminów).

### 2. PIM (Privileged Identity Management)
Narzędzie realizujące zasadę **Just-In-Time (JIT) Access**. Zamiast dawać komuś rolę administratora "na zawsze", użytkownik staje się uprawniony (*Eligible*) do jej aktywacji. Gdy potrzebuje coś naprawić, wnioskuje o aktywację roli np. na 2 godziny, podając uzasadnienie i przechodząc dodatkowe MFA. Po 2 godzinach uprawnienia wygasają automatycznie.

> [!IMPORTANT] Co monitorujesz w SOC?
> W Microsoft Sentinel monitorujesz logi auditowe pod kątem anomalii w RBAC:
> * Nagłe nadanie roli *Owner* lub *Global Administrator* nowemu, nieznanemu kontu.
> * Częste aktywacje ról w PIM poza godzinami pracy pracownika (możliwe przejęcie konta).