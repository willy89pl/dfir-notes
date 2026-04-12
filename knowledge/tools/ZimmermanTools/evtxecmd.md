---
tags:
  - tool
  - forensics
  - windows
  - evtx
  - event-logs
  - csv
  - zimmermantools
category: tools
platform: windows
---

# EvtxECmd

Narzędzie Erica Zimmermana do parsowania plików Windows Event Log (`.evtx`) do formatu CSV.

---

## Użycie

```bash
# Pojedynczy plik
EvtxECmd.exe -f "plik.evtx" --csv output_folder

# Cały folder z logami
EvtxECmd.exe -d "C:\Windows\System32\winevt\Logs" --csv output_folder
```

---

## Output

Generuje pliki CSV które najwygodniej przeglądać w [[timeline-explorer]].

---

## Gdzie pobrać

Część pakietu **Eric Zimmerman Tools**:
```
https://ericzimmerman.github.io/
```
