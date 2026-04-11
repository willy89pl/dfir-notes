---
tags: [active-directory, adcs, privilege-escalation, attack, detection]
category: active-directory
---

# AD CS — ESC1 (Active Directory Certificate Services)

Atak eskalacji uprawnień wykorzystujący błędną konfigurację szablonu certyfikatu w AD CS, pozwalający uzyskać certyfikat dowolnego użytkownika (w tym Administratora) i wymienić go na TGT + NT hash.

---

## Podstawowe pojęcia

| Pojęcie | Opis |
|---------|------|
| **Certificate Authority (CA)** | Urząd certyfikacji — serwer wystawiający certyfikaty w domenie |
| **Certificate Template** | Szablon certyfikatu — definiuje kto może prosić o certyfikat i do czego służy |
| **Certificate Signing Request (CSR)** | Prośba o wystawienie certyfikatu składana do CA |
| **Extended Key Usage (EKU)** | Określa do czego certyfikat może być użyty (np. logowanie, szyfrowanie) |
| **Subject Alternative Name (SAN)** | Alternatywna tożsamość wpisana w certyfikacie |
| **PKINIT** | Mechanizm Kerberosa pozwalający uwierzytelnić się certyfikatem zamiast hasłem |

---

## Jak działa uwierzytelnianie certyfikatem w AD

1. Użytkownik składa **CSR** do CA na podstawie szablonu
2. CA wystawia certyfikat (plik `.pfx` — certyfikat + klucz prywatny)
3. Użytkownik prezentuje certyfikat DC przez **PKINIT**
4. DC zwraca **TGT** + **NT hash** konta (mechanizm **UnPAC-the-Hash**)
5. Atakujący używa TGT (Pass-the-Ticket) lub NT hash (Pass-the-Hash)

---

## Warunki podatności ESC1

| Warunek | Opis |
|---------|------|
| **Enrollee Supplies Subject** | Wnioskodawca sam wpisuje wartość SAN |
| **Client Authentication w EKU** | Certyfikat służy do logowania do domeny |
| **Niskie uprawnienia do enrollment** | Zwykły użytkownik domenowy może prosić o certyfikat |

Atakujący wpisuje w SAN tożsamość Administratora (`upn=administrator@domena.local`) — CA wystawia certyfikat bez weryfikacji.

---

## Łańcuch ataku ESC1

1. Rekonesans — znalezienie podatnego szablonu
2. Złożenie CSR z `SAN = Administrator`
3. Otrzymanie certyfikatu `.pfx`
4. Wymiana certyfikatu na TGT + NT hash przez PKINIT
5. Dostęp do domeny jako Administrator

---

## Narzędzia

| Etap | Narzędzie |
|------|-----------|
| Rekonesans szablonów | **Certipy** / **Certify** / **BloodHound** |
| Złożenie CSR (ESC1) | **Certipy req** |
| Wymiana certyfikatu na TGT | **Certipy auth** |
| Użycie NT hash | **Pass-the-Hash** (psexec, wmiexec) |

```bash
# Rekonesans — znajdź podatne szablony
certipy find -u user@domena.local -p hasło -dc-ip <IP>

# ESC1 — złóż CSR z SAN Administratora
certipy req -u user@domena.local -p hasło \
  -ca "nazwa-CA" \
  -template "PodatnyRuleSzablon" \
  -upn "Administrator@domena.local" \
  -dc-ip <IP>

# Wymień certyfikat na TGT + NT hash
certipy auth -pfx administrator.pfx -dc-ip <IP>
```

---

## Detekcja w Splunk

| Event ID | Źródło | Co rejestruje |
|----------|--------|---------------|
| **4886** | CA | Żądanie certyfikatu (CSR) |
| **4887** | CA | Certyfikat wystawiony |
| **4888** | CA | Żądanie odrzucone |
| **4768** | DC | TGT request — widać użycie PKINIT |

Główny wskaźnik — wnioskodawca różni się od tożsamości w SAN:

```spl
index=wineventlog EventCode=4887
| rex field=Message "Certificate Template: (?<template>[^\r\n]+)"
| rex field=Message "Subject: (?<subject>[^\r\n]+)"
| rex field=Message "Requester: (?<requester>[^\r\n]+)"
| where requester != subject
| table _time, requester, subject, template
```

---

## Red flags w szablonach

- `CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT` — wnioskodawca kontroluje SAN
- **EKU:** `Client Authentication` / `Smart Card Logon` / `Any Purpose`
- **Enrollment rights** dla `Domain Users` lub `Authenticated Users`
