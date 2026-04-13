---
tags: [tool, forensic, windows, ntfs, filesystem]
category: tools
platform: windows
---

# NTFS Log Tracker

Narzędzie do parsowania artefaktów systemu plików NTFS — $MFT([[mft]]), $LogFile([[logfile]]) i $UsnJrnl([[usnjrnl]]) — pozwalające na odtworzenie pełnej historii operacji na plikach.

---

## Gdzie pobrać

```
https://sites.google.com/site/forensicnote/ntfs-log-tracker
```

---

## Obsługiwane pliki

| Plik | Opis |
|------|------|
| **$MFT** | Master File Table |
| **$LogFile** | Dziennik transakcji NTFS |
| **$UsnJrnl** | Dziennik zmian USN |

---

## Użycie

Aplikacja GUI — wskazujesz pliki $MFT, $LogFile i $UsnJrnl, narzędzie parsuje je razem i generuje chronologiczny timeline operacji na plikach.

---

## Kluczowe operacje które wykrywa

| Operacja | Opis |
|----------|------|
| `Data_Overwrite` | Nadpisanie zawartości pliku |
| `FileCreate` | Utworzenie pliku |
| `FileDelete` | Usunięcie pliku |
| `RenameOldName / RenameNewName` | Zmiana nazwy pliku |

---

## Kiedy używać

Gdy samo $MFT nie daje pełnego obrazu — szczególnie przy:
- Wykrywaniu nadpisania pliku (`Data_Overwrite`)
- Analizie usuniętych plików
- Korelacji operacji z precyzyjnym timestampem
