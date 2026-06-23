---
tags:
  - sc-200
  - microsoft
  - identity
  - entraconnect
  - hybrid
category: sc-200
---

# Microsoft Entra Connect — Charakterystyka (Pod SC-200)

**Microsoft Entra Connect** (dawniej *Azure AD Connect*) to bezpłatne narzędzie instalowane na lokalnym serwerze (On-premises), które służy do synchronizacji obiektów (użytkowników, grup, kontaktów) z lokalnej bazy **Active Directory (AD DS)** do chmurowego repozytorium **Microsoft Entra ID**. 

Dzięki niemu firma uzyskuje **tożsamość hybrydową** – pracownik ma jedno i to samo konto (login i hasło) zarówno do komputera w biurze, jak i do usług chmurowych (M365, Azure).

---

## Trzy Metody Uwierzytelniania w Entra Connect

Podczas konfiguracji synchronizacji administrator musi wybrać, w jaki sposób chmura będzie sprawdzać hasła użytkowników. Musisz je znać na egzamin:

1. **Password Hash Synchronization (PHS) — Synchronizacja skrótów haseł:**
   * **Jak działa:** Entra Connect pobiera skróty (hashe) haseł z lokalnego AD, dodatkowo je szyfruje (SHA256) i wysyła do chmury Entra ID. Logowanie odbywa się w 100% w chmurze.
   * **Perspektywa SC-200:** Najbardziej zalecana metoda przez Microsoft. Umożliwia działanie *Microsoft Entra ID Protection* (wykrywanie, czy hasła użytkowników nie wyciekły do darknetu).
2. **Pass-Through Authentication (PTA) — Uwierzytelnianie bezpośrednie:**
   * **Jak działa:** Hasła nigdy nie trafiają do chmury. Gdy użytkownik loguje się w chmurze, Entra ID przesyła zaszyfrowane zapytanie do specjalnego agenta zainstalowanego lokalnie w firmie, który sprawdza hasło bezpośrednio w lokalnym Kontrolerze Domeny.
3. **Federacja (np. za pomocą AD FS):**
   * **Jak działa:** Cały proces logowania jest przekierowywany do lokalnej infrastruktury Active Directory Federation Services (AD FS).

---

## Perspektywa SC-200 (Cybersecurity / SecOps)

Z punktu widzenia analityka SOC, serwer z zainstalowanym Entra Connect to jeden z **najbardziej krytycznych i wrażliwych punktów w całej firmie (tzw. Tier 0)**.

### 1. Ryzyko Ataku "Identity Blending" / Lateral Movement
Jeśli haker włamie się do lokalnej sieci firmowej i przejmie uprawnienia administratora na serwerze Entra Connect, może zmodyfikować proces synchronizacji. 
* Może np. stworzyć lokalnie fałszywe konto z uprawnieniami administratora, które zsynchronizuje się do chmury, dając mu pełną kontrolę nad Twoim Azurem i M365 (ruch typu *On-prem to Cloud attack*).

### 2. Ataki na konto synchronizacyjne (ADSync)
Podczas instalacji, Entra Connect tworzy w lokalnym AD specjalne konto (zazwyczaj zaczynające się od `MSOL_`), które posiada wysokie uprawnienia do odczytu i replikacji danych (w tym haseł wszystkich użytkowników). To konto jest częstym celem ataków typu **DCSync**, mających na celu wyciągnięcie bazy haseł `NTDS.dit`.

### 3. Co monitorujesz w SOC (Wykrywanie zagrożeń w Sentinel)?
Większość incydentów związanych z Entra Connect wykrywasz za pomocą narzędzia **Microsoft Defender for Identity (MDI)** oraz logów z Entra ID:
* **Modyfikacje konta synchronizacyjnego:** Każda nagła zmiana uprawnień lub próba bezpośredniego logowania na konto `MSOL_` powinna natychmiast wywołać alarm najwyższego stopnia (High Alert).
* **Anomalie w masowej synchronizacji:** Nagłe usunięcie lub zmiana tysięcy kont użytkowników w jednym cyklu synchronizacji (co może oznaczać sabotaż lub ransomware niszczący tożsamości).
* **Logi Azure AD Connect Health:** Monitorujesz stan zdrowia agenta synchronizacji pod kątem prób manipulacji przy usłudze lub zablokowania komunikacji z chmurą.

> [!IMPORTANT] Zapamiętaj pod egzamin
> Serwer Microsoft Entra Connect musi być chroniony tak samo rygorystycznie jak Kontroler Domeny (DC). Do jego monitorowania na poziomie OS używa się **Microsoft Defender for Endpoint (MDE)**, a ruch tożsamościowy analizuje **Microsoft Defender for Identity (MDI)**.