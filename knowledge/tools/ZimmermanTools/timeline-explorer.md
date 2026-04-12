---
tags:
  - tool
  - forensics
  - windows
  - zimmermantools
  - csv
  - registry
category: tools
platform: windows
---

# Timeline Explorer

Narzędzie Erica Zimmermana do przeglądania plików CSV generowanych przez inne parsery z pakietu EZ Tools — EvtxECmd, PECmd, AmcacheParser i inne.

---

## Do czego służy

Zastępuje otwieranie CSV w Excelu — oferuje wygodne filtrowanie, sortowanie i kolorowanie wierszy, co jest kluczowe przy analizie dużych zbiorów danych forensycznych.

---

## Użycie

Aplikacja GUI — przeciągasz plik CSV lub otwierasz przez **File → Open**.

Przydatne funkcje:
- **Filtrowanie** po dowolnej kolumnie (Ctrl+F)
- **Kolorowanie wierszy** — ręczne oznaczanie istotnych wpisów
- **Pinowanie kolumn** — przydatne przy szerokich CSV

---

## Typowe workflow

```
PECmd / EvtxECmd / AmcacheParser
        ↓
    plik CSV
        ↓
  Timeline Explorer
```

---

## Gdzie pobrać

Część pakietu **Eric Zimmerman Tools**:
```
https://ericzimmerman.github.io/
```
