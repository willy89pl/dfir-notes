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

Plik ukryty i chroniony przez system — dostępny przez narzędzia forensic lub KAPE.

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
### Pliki rezydualne (Resident Files)
*pliki nazywamy **rezydualnymi** (ang. _resident_), ponieważ "mieszkają" wewnątrz tabeli MFT*

Jeśli plik jest bardzo mały (zazwyczaj poniżej **700-800 bajtów**), NTFS nie zapisuje go w osobnym klastrze na dysku(bo to marnotrawstwo przestrzeni), ale umieszcza jego zawartość **bezpośrednio w rekordzie MFT** (w atrybucie `$DATA`).

- **Znaczenie:** Nawet jeśli plik został "bezpiecznie" usunięty z dysku poprzez nadpisanie wolnego miejsca, jego treść może nadal istnieć wewnątrz MFT, jeśli był plikiem rezydualnym.
    
Łatwo to przejrzeć w [[r-studio]]
### Ślady po usuniętych plikach

Gdy plik jest usuwany, system nie kasuje rekordu MFT, a jedynie oznacza go jako "wolny" (flaga w nagłówku rekordu zmienia się z 1 na 0).

- Dopóki system nie potrzebuje miejsca na nowy plik, cały rekord starego pliku (nazwa, daty, lokalizacja danych) pozostaje nienaruszony i gotowy do odzyskania.
    

### Artefakty transakcyjne

MFT ściśle współpracuje z plikiem `$LogFile`, co pozwala na odtworzenie ostatnich operacji na systemie plików, nawet jeśli same pliki zniknęły.
## Jak przeglądać

**[[mftecmd]]** (Eric Zimmerman):
```bash
MFTECmd.exe -f "$MFT" --csv output_folder
```

Następnie przeglądanie w [[timeline-explorer]].

**[[ntfs-log-tracker]]** — parsuje [[mft]] razem z [[usnjrnl]] i [[logfile]] dając pełny chronologiczny timeline operacji na plikach.
