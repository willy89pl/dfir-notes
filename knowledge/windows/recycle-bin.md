---
tags:
  - forensics
  - windows
  - artifact
  - history
  - recyclebin
category: windows
platform: windows
---

# Recycle Bin Forensics ($Recycle.Bin)

## Lokalizacja

```
C:\$Recycle.Bin\S-1-5-21-<SID>
```

- Każdy katalog `S-1-5-21-...` odpowiada konkretnemu użytkownikowi (SID)
- SID można zmapować do użytkownika (np. `Administrator`)

---

## Typy plików w koszu

### `$I` – metadata (INFO)

Pliki `$I` zawierają informacje o usuniętym pliku:

- Timestamp usunięcia
- Oryginalna ścieżka pliku
- Rozmiar pliku

Format binarny – wymaga parsera

---

### `$R` – real data

Pliki `$R` to:

- rzeczywista zawartość usuniętego pliku
- nazwy losowe (np. `$R1A2B3.txt`)
- można je otwierać jak normalne pliki

---

## Relacja `$I` ↔ `$R`

- `$Ixxxxx` ↔ `$Rxxxxx` (ten sam identyfikator)
- `$I` opisuje plik
- `$R` zawiera dane

Przykład:

```
$IABC123.txt  → metadata
$RABC123.txt  → właściwy plik
```

---

## Znaczenie forensic

Recycle Bin jest bardzo cenny, bo:

- pokazuje co użytkownik próbował ukryć
- umożliwia odzyskanie usuniętych plików
- pozwala budować timeline zdarzeń

---

## Edge cases

- `$R` bez `$I` – brak metadanych (utrudniona analiza)
- `$I` bez `$R` – plik mógł zostać nadpisany lub trwale usunięty
- wiele SID – wielu użytkowników na systemie
- pliki mogą być częściowo nadpisane lub uszkodzone
- duże pliki mogą być niekompletne

---

## Wskazówki (CTF / DFIR)

- analizuj `$R` pod kątem:
    - plików wykonywalnych (`.exe`)
    - skryptów (`.ps1`, `.bat`)
    - archiwów (`.zip`, `.rar`)
- użyj `$I`, aby odzyskać oryginalne nazwy i ścieżki
- łącz dane z innymi artefaktami (Prefetch, Amcache, LNK)
- sprawdzaj timestampy pod kątem korelacji zdarzeń