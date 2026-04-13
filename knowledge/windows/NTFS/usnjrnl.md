---
tags: [windows, artifact, forensics, ntfs, filesystem]
category: windows
platform: windows
---

# $UsnJrnl (Update Sequence Number Journal)

Chronologiczny dziennik wszystkich zmian na systemie plików NTFS — rejestruje każdą operację na plikach i folderach z precyzyjnym timestampem. Jeden z najcenniejszych artefaktów forensycznych systemu plików.

> Pełny obraz operacji na plikach daje dopiero trio: **[[mft]] + [[logfile]] + [[usnjrnl]]**

---

## Lokalizacja

```
C:\$Extend\$UsnJrnl
```

---

## Co rejestruje

Każdy wpis zawiera:
- Nazwę pliku
- Ścieżkę
- Timestamp operacji
- **Typ operacji** — to kluczowa wartość

### Kluczowe typy operacji (Reason)

| Reason | Opis |
|--------|------|
| `FileCreate` | Plik został utworzony |
| `FileDelete` | Plik został usunięty |
| `DataOverwrite` | Zawartość pliku została nadpisana |
| `DataExtend` | Zawartość pliku została rozszerzona |
| `DataTruncation` | Zawartość pliku została skrócona |
| `RenameOldName` | Stara nazwa przed zmianą |
| `RenameNewName` | Nowa nazwa po zmianie |
| `SecurityChange` | Zmiana uprawnień |

---

## Dlaczego cenniejszy niż $MFT

$MFT pokazuje stan końcowy — $UsnJrnl pokazuje **historię zmian**. Szczególnie przydatny gdy:
- Plik został nadpisany (`DataOverwrite`) — $MFT nie pokaże że to nastąpiło
- Plik zmienił nazwę — widać starą i nową nazwę
- Plik został usunięty i nadpisany przez system

---

## Ograniczenia

- Nadpisywany cyklicznie gdy osiągnie maksymalny rozmiar
- Domyślny rozmiar to zazwyczaj 32 MB — przy dużej aktywności starsze wpisy są nadpisywane szybko

---

## Jak przeglądać

**[[mftecmd]]** (Eric Zimmerman) obsługuje też $UsnJrnl:
```bash
MFTECmd.exe -f "$UsnJrnl" --csv output_folder
```

Następnie przeglądanie w [[timeline-explorer]].

**[[ntfs-log-tracker]]** — parsuje $UsnJrnl razem z [[$MFT]] i [[$LogFile]] dając pełny chronologiczny timeline.
