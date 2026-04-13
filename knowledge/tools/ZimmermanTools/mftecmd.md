---
tags:
  - tool
  - forensics
  - windows
  - mft
  - ntfs
  - zimmermantools
category: tools
platform: windows
---

# MFTECmd

Narzędzie Erica Zimmermana do parsowania pliku [[mft]] (Master File Table) systemu NTFS do formatu CSV.

---

## Użycie

```bash
# Parsowanie $MFT
MFTECmd.exe -f "$MFT" --csv output_folder

# Parsowanie z dodatkowym $Boot
MFTECmd.exe -f "$MFT" --vss --csv output_folder
```

---

## Output

Generuje plik CSV który najwygodniej przeglądać w [[timeline-explorer]].
Szukaj po nazwie pliku lub ścieżce — znajdziesz dokładne timestampy NTFS z precyzją do sekundy.

---

## Gdzie pobrać

Część pakietu **Eric Zimmerman Tools**:
```
https://ericzimmerman.github.io/
```
