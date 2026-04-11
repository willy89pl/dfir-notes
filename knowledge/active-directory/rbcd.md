---
tags: [active-directory, kerberos, privilege-escalation, lateral-movement, attack, detection]
category: active-directory
---

# Resource-Based Constrained Delegation (RBCD)

Atak eskalacji uprawnień w AD wykorzystujący mechanizm delegacji Kerberosa — pozwala impersonować dowolnego użytkownika (w tym Administratora) wobec docelowej usługi.

---

## Trzy typy delegacji w AD

| Typ | Konfiguracja | Zakres |
|-----|-------------|--------|
| **Unconstrained Delegation** | Na koncie usługowym | Może impersonować wobec **dowolnej** usługi, trzyma TGT usera |
| **Constrained Delegation (KCD)** | Na koncie usługowym (wymaga admina domeny) | Tylko wobec **zdefiniowanej listy** usług, używa S4U |
| **Resource-Based (RBCD)** | Na **obiekcie docelowym** (wystarczy GenericWrite) | Tylko wobec tego zasobu, używa S4U |

Kluczowa różnica:
```
KCD:   [Konto usługowe] ← atrybut: "mogę iść do: SQL"
RBCD:  [Zasób docelowy] ← atrybut: "wpuszczam: EvilComputer$"
```

---

## Mechanizm S4U (używany w KCD i RBCD)

**S4U2Self** — konto usługowe prosi DC o bilet jakby wskazany użytkownik pytał o to konto.

**S4U2Proxy** — konto usługowe używa tego biletu żeby poprosić DC o TGS do usługi docelowej w imieniu użytkownika.

```
EvilComputer$ → DC: S4U2Self  → bilet "Administrator → EvilComputer$"
EvilComputer$ → DC: S4U2Proxy → bilet "Administrator → TargetPC"
EvilComputer$ → TargetPC:       dostęp jako Administrator!
```

---

## Wymagania do ataku

- Dowolne konto domenowe
- Uprawnienie **GenericWrite / WriteProperty** na obiekcie docelowego komputera w AD
- `ms-DS-MachineAccountQuota > 0` (domyślnie = 10)

---

## Łańcuch ataku

1. Zdobyć konto domenowe (własne lub przejęte)
2. Sprawdzić czy konto ma **GenericWrite** na docelowym obiekcie (BloodHound)
3. Dodać **EvilComputer$** do domeny — wykorzystując `MachineAccountQuota`
4. Wpisać `EvilComputer$` do atrybutu `msDS-AllowedToActOnBehalfOfOtherIdentity` na docelowym komputerze
5. **S4U2Self** — EvilComputer$ generuje bilet jakby Administrator pytał
6. **S4U2Proxy** — EvilComputer$ dostaje TGS do docelowej usługi jako Administrator
7. Użyć biletu → dostęp jako Administrator

---

## Narzędzia

| Etap | Narzędzie |
|------|-----------|
| Rekonesans uprawnień | **BloodHound** / **PowerView** |
| Dodanie komputera | **Impacket addcomputer.py** / **PowerMad** |
| Modyfikacja atrybutu | **PowerView** (`Set-ADComputer`) |
| S4U2Self + S4U2Proxy | **Impacket getST.py** / **Rubeus** |
| Użycie biletu | **Pass-the-Ticket** → psexec, wmiexec |

---

## Detekcja w Splunk

| Event ID | Co rejestruje |
|----------|---------------|
| **4741** | Utworzenie konta komputerowego (EvilComputer$) |
| **4742** | Modyfikacja konta komputerowego (zmiana atrybutu) |
| **4769** | Żądanie TGS — S4U2Self i S4U2Proxy |

Kluczowy wskaźnik S4U2Proxy — pole `TransmittedServices` w EventID 4769 jest wypełnione:

```spl
index=wineventlog EventCode=4769
| where TransmittedServices != "-"
| table _time, Account_Name, Service_Name, TransmittedServices, Client_Address
```

Korelacja całego łańcucha:
```spl
index=wineventlog EventCode IN (4741, 4742, 4769)
| stats count by EventCode, Account_Name, src_ip
| sort src_ip, EventCode
```

