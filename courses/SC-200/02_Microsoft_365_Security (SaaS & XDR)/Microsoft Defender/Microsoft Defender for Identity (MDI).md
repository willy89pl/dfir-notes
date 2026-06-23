---
tags:
  - sc-200
  - microsoft
  - defender
  - xdr
  - security
category: sc-200
---
---
tags:
  - SC-200
  - Defender
  - Identity
  - ActiveDirectory
---
# Microsoft Defender for Identity (MDI)

Microsoft Defender for Identity (MDI) (dawniej *Azure Advanced Threat Protection / AATP*) to rozwiązanie klasy ITDR (Identity Threat Detection and Response). Służy do monitorowania, analizowania i zabezpieczania lokalnej infrastruktury tożsamości (**on-premises Active Directory**, AD FS oraz serwerów Entra Connect).

---

## Zasada Działania i Architektura

MDI nie posiada tradycyjnego agenta na komputerach użytkowników. Działa bezpośrednio na serwerach tożsamości:
1. **MDI Sensors (Sensory):** Lekkie oprogramowanie instalowane bezpośrednio na **Kontrolerach Domeny (Domain Controllers)**.
2. **Analiza ruchu:** Sensor parsuje ruch sieciowy (protokoły Kerberos, NTLM, DNS, RPC) oraz zbiera zdarzenia z dziennika Windows Event Log.
3. **Chmura:** Przetworzone metadane trafiają do chmury, gdzie silnik behawioralny tworzy profil normalnego zachowania dla każdego konta i wykrywa anomalie.

## Fazy Ataku Wykrywane przez MDI

Scenariusze SC-200 budowane są wokół konkretnych technik hakerskich z matrycy MITRE ATT&CK:

* **Reconnaissance (Rekonesans):** Próby mapowania sieci i struktury AD przez napastnika (np. zapytania *DNS Reconnaissance*, enumeracja kont użytkowników przez protokół SAMR).
* **Compromised Credentials (Przejęcie poświadczeń):** Wykrywanie ataków siłowych (*Brute-force*), prób wyciągania haseł z pamięci procesów oraz ataków typu *Dictionary attacks*.
* **Lateral Movement (Ruch boczny):** Wykrywanie przemieszczania się hakera między serwerami (np. ataki *Pass-the-Ticket*, *Pass-the-Hash*, nadużycia protokołu NTLM).
* **Domain Dominance (Przejęcie domeny):** Wykrywanie krytycznych ataków dążących do uprawnień Domain Admin, takich jak *Golden Ticket*, *Silver Ticket* czy manipulacje strukturą replikacji (*DCSync*).

> [!TIP] Korelacja z chmurą
> MDI ściśle współpracuje z **Microsoft Entra ID Protection**. Jeśli MDI wykryje atak *Pass-the-Hash* na lokalnym kontrolerze domeny, automatycznie prześle sygnał do chmury, podnosząc poziom parametru **User Risk** do wartości **High** dla skorelowanego konta użytkownika w Entra ID.