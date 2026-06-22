---
tags:
  - active-directory
  - sc-200
  - microsoft
category: active-directory
---

# Active Directory (AD) — Charakterystyka

**Active Directory (AD)**, a dokładniej **Active Directory Domain Services (AD DS)**, to stworzona przez Microsoft usługa katalogowa (Directory Service) dla sieci systemów Windows. Działa jako **centralna baza danych** oraz system zarządzania tożsamością i uprawnieniami w środowisku lokalnym (on-premises). ***ON-PREM !***

---

## 🔑 Kluczowe Funkcje AD

* **Zarządzanie Tożsamościami (Identity Management):** Centralne repozytorium użytkowników, komputerów, grup, drukarek i innych zasobów sieciowych.
* **Uwierzytelnianie i Autoryzacja:** 
	* **Uwierzytelnianie (Authentication):** Sprawdzenie, kim jesteś (np. logowanie hasłem/kartą). Głównym protokołem jest tu **Kerberos** (oraz starszy NTLM).
	* **Autoryzacja (Authorization):** Sprawdzenie, do czego masz dostęp (np. czy dany użytkownik może otworzyć folder na dysku sieciowym).
* **Zarządzanie Centralne (GPO):** Za pomocą **Group Policy Objects (GPO)** administratorzy mogą masowo konfigurować komputery w sieci (np. wymusić zmianę haseł, zablokować USB, zainstalować oprogramowanie).

---

## 🗂️ Struktura Logiczna Active Directory

AD organizuje zasoby w hierarchiczną strukturę:

1. **Obiekty (Objects):** Podstawowe jednostki, np. pojedynczy użytkownik (`User`), komputer (`Computer`) lub drukarka.
2. **Jednostki Organizacyjne (OU - Organizational Units):** "Foldery" wewnątrz AD, do których wkłada się obiekty, aby łatwiej nimi zarządzać i przypisywać do nich polisy GPO.
3. **Domena (Domain):** Główna granica administracyjna (np. `firma.local`). Wszystkie obiekty w domenie współdzielą tę samą bazę danych.
4. **Drzewo (Tree):** Zbiór domen, które dzielą wspólną przestrzeń nazw (np. `dev.firma.local` i `prod.firma.local`).
5. **Las (Forest):** Najwyższy poziom hierarchii. Zbiór jednego lub więcej drzew, które współdzielą wspólny schemat bazy danych i konfigurację, ale mogą mieć różne nazwy (np. `firma.local` i `sklep.pl`).

---

## 🛡️ Perspektywa SC-200 (Cybersecurity / SecOps)

Jako analityk SOC (Security Operations Analyst), musisz patrzeć na lokalne AD jako na **główny cel większości zaawansowanych ataków hakerskich**.

### Typowe wektory ataków na AD:
* **Kerberoasting:** Atak na bilety protokołu Kerberos w celu złamania haseł kont usługowych (Service Accounts).
* **Pass-the-Hash (PtH) / Pass-the-Ticket (PtT):** Kradzież skrótów haseł lub biletów z pamięci RAM komputera w celu zalogowania się na inne maszyny bez znajomości hasła jawnego.
* **DCShadow / DCSync:** Ataki polegające na podszyciu się pod Kontroler Domeny w celu zreplikowania i wykradzenia bazy haseł (`NTDS.dit`).

### Jak Microsoft chroni AD (i co monitorujesz w SC-200):
* **Microsoft Defender for Identity (MDI):** Chmurowe narzędzie bezpieczeństwa, które analizuje ruch sieciowy z lokalnych Kontrolerów Domeny, wykrywając anomalie, podejrzane logowania i techniki hakerskie (np. rekonesans AD, lateral movement).

---

## 🔄 AD (Lokalne) a Microsoft Entra ID (Dawne Azure AD)

| Cecha | Active Directory (AD DS) | Microsoft Entra ID |
| :--- | :--- | :--- |
| **Środowisko** | Lokalne (On-premises / Serwery) | Chmurowe (Cloud-native) |
| **Protokoły** | Kerberos, NTLM, LDAP | OAuth 2.0, SAML, OIDC, Graph API |
| **Zarządzanie** | GPO (Group Policy) | MDM / Microsoft Intune |
| **Struktura** | Hierarchiczna (OUs, Domeny, Lasy) | Płaska (Użytkownicy i Grupy w Tenant) |

> [!NOTE] Ważne pod egzamin
> W nowoczesnych firmach stosuje się **środowisko hybrydowe**, gdzie lokalne AD jest synchronizowane do chmury za pomocą narzędzia **Microsoft Entra Connect**. Hakerzy często próbują uderzyć w lokalne AD, aby stamtąd dostać się do chmury (tzw. *Identity Blending*).