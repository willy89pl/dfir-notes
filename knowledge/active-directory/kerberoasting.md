---
tags: [active-directory, kerberos, credential-access, attack, detection]
category: active-directory
---

# Kerberoasting

Atak polegający na wyłudzeniu biletów **TGS (Ticket Granting Service)** dla kont z ustawionym **SPN (Service Principal Name)**, a następnie złamaniu ich offline.

---

## Jak działa?

1. Atakujący (zwykły użytkownik domenowy) pyta DC o bilet TGS dla konta usługowego
2. DC wydaje bilet zaszyfrowany **hashem konta usługowego**
3. Atakujący zabiera bilet i łamie go offline
4. Rezultat: hasło konta usługowego

---

## Kluczowe szczegóły

- Nie potrzeba uprawnień admina — wystarczy **dowolne konto domenowe**
- Atakujący celowo żąda szyfrowania **RC4 (0x17)** zamiast AES — łatwiejsze do złamania
- Możliwy **downgrade szyfrowania** — nawet jeśli konto obsługuje AES, można poprosić o RC4
- Atakowane konta muszą mieć ustawiony **SPN**

---

## Detekcja

| Event ID | Co rejestruje |
|----------|---------------|
| **4769** | Żądanie TGS — główny wskaźnik |

Podejrzane sygnały:
- Dużo żądań 4769 z jednego IP
- `TicketEncryptionType = 0x17` (RC4)
- `ServiceName` nie kończy się na `$`

```spl
index=wineventlog EventCode=4769
Ticket_Encryption_Type=0x17
Service_Name!="*$"
| stats count by Account_Name, Client_Address, Service_Name
| where count > 5
| sort - count
```

---

## Narzędzia

| Etap | Narzędzie |
|------|-----------|
| Wyciąganie biletów | **Rubeus** / **Impacket GetUserSPNs.py** |
| Łamanie hashy | **Hashcat** / **John the Ripper** |

