---
tags: [windows, artifact, forensics, ntfs, filesystem]
category: windows
platform: windows
---

# $MFT (Master File Table)

Kluczowy artefakt systemu plików NTFS — baza danych zawierająca wpis dla każdego pliku i folderu na wolumenie. Każdy wpis zawiera metadane pliku włącznie z dokładnymi timestampami.

> Pełny obraz operacji na plikach daje dopiero trio: **[[mft]] + [[logfile]] + [[usnjrnl]]**

---

## Lokalizacja

```
C:\$MFT
```

Plik ukryty i chroniony przez system — dostępny przez narzędzia forensyczne lub KAPE.

---

## Co zawiera

Każdy wpis w $MFT zawiera dwa zestawy czterech timestampów NTFS:

| Timestamp | Opis |
|-----------|------|
| **Created** | Czas utworzenia pliku |
| **Modified** | Czas ostatniej modyfikacji zawartości |
| **Last Record Change** | Czas ostatniej zmiany metadanych |
| **Accessed** | Czas ostatniego dostępu |

---

## Dwa zestawy timestampów — 0x10 vs 0x30

| Atrybut | Nazwa | Wiarygodność |
|---------|-------|-------------|
| **0x10** | `$STANDARD_INFORMATION` | Łatwiejszy do modyfikacji — widoczny w Explorerze |
| **0x30** | `$FILE_NAME` | Trudniejszy do modyfikacji — bardziej wiarygodny |

Rozbieżność między 0x10 i 0x30 to sygnał **timestompingu** — manipulacji timestampami przez atakującego.

---

## Ograniczenia $MFT

- Pokazuje **stan końcowy** — nie rejestruje historii zmian
- Usunięte pliki pozostają jako nieaktywne wpisy dopóki nie zostaną nadpisane
- Nie rejestruje wprost operacji takich jak `Data_Overwritten` — do tego potrzebny [[$UsnJrnl]]

---

## Jak przeglądać

**[[mftecmd]]** (Eric Zimmerman):
```bash
MFTECmd.exe -f "$MFT" --csv output_folder
```

Następnie przeglądanie w [[timeline-explorer]].

**[[ntfs-log-tracker]]** — parsuje $MFT razem z [[$LogFile]] i [[$UsnJrnl]] dając pełny chronologiczny timeline operacji na plikach.
