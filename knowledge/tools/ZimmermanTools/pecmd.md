---
tags:
  - tool
  - forensics
  - windows
  - prefetch
  - execution
  - zimmermantools
  - csv
category: tools
platform: windows
---

# PECmd

Narzędzie Erica Zimmermana do parsowania plików [[prefetch]] (`.pf`) do formatu CSV.

---

## Użycie

```bash
# Pojedynczy plik
PECmd.exe -f "MSEDGE.EXE-A1B2C3D4.pf" --csv output_folder

# Cały folder Prefetch
PECmd.exe -d "C:\Windows\Prefetch" --csv output_folder
```

---

## Output

Generuje pliki CSV które najwygodniej przeglądać w [[timeline-explorer|timeline-explorer]]

---

## Gdzie pobrać

Część pakietu **Eric Zimmerman Tools**:
```
https://ericzimmerman.github.io/
```
