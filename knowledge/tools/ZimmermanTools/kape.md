---
tags:
  - tool
  - forensic
  - windows
  - ntfs
  - filesystem
  - zimmermantools
category: tools
platform: windows
---

# KAPE (Kroll Artifact Parser and Extractor)

## Wprowadzenie

KAPE (Kroll Artifact Parser and Extractor) to narzędzie DFIR służące do:

- szybkiego zbierania artefaktów z systemu
- parsowania danych do czytelnej formy

Autor: Eric Zimmerman

---

## Główne funkcje

### 1. Target Collection (zbieranie danych)

- kopiuje wybrane artefakty z systemu
- działa bardzo szybko (priorytet: minimalny czas akwizycji)
- używa zdefiniowanych "Targets"

Przykłady:

- Recycle Bin
- Prefetch
- Registry hives
- Event Logs

---

### 2. Module Execution (parsowanie)

- uruchamia parsery na zebranych danych
- generuje wyniki w formacie:
    - CSV
    - JSON
    - TXT

Przykłady parserów:

- RECmd (Registry)
- PECmd (Prefetch)
- EvtxECmd (Event Logs)

---

## Struktura KAPE

### Targets

- definiują CO zbierać
- pliki `.tkape`

Przykład:

- `RecycleBin.tkape`
- `WindowsPrefetch.tkape`

---

### Modules

- definiują JAK analizować dane
- pliki `.mkape`

Przykład:

- `RecycleBin` parser
- `Amcache` parser

---

## Podstawowe użycie

### Tryb akwizycji

```
kape.exe --tsource C: --tdest E:\output --target RecycleBin
```

### Tryb parsowania

```
kape.exe --msource E:\output --mdest E:\parsed --module RecycleBin
```

---

## Kluczowe parametry

- `--tsource` – źródło danych (np. dysk)
- `--tdest` – gdzie zapisać zebrane artefakty
- `--target` – jakie dane zbierać
- `--msource` – źródło do parsowania
- `--mdest` – gdzie zapisać wyniki
- `--module` – jakie parsery uruchomić

---

## Tryb "Compound" (najczęściej używany)

```
kape.exe --tsource C: --tdest E:\out --target !BasicCollection --mdest E:\parsed --module !BasicCollection
```

- zbiera i parsuje w jednym kroku
- `!BasicCollection` = zestaw najważniejszych artefaktów

---

## Zalety

- bardzo szybki
- modularny (Targets + Modules)
- szerokie wsparcie artefaktów Windows
- łatwa automatyzacja

---

## Ograniczenia

- nie analizuje "na żywo" (tylko zbiera i parsuje)
- wymaga znajomości artefaktów
- wyniki trzeba interpretować ręcznie

---

## Zastosowanie (DFIR / CTF)

- analiza incydentów bezpieczeństwa
- budowanie timeline zdarzeń
- odzyskiwanie aktywności użytkownika
- szybkie triage systemu

---

## Typowy workflow

1. Uruchom KAPE (zbieranie artefaktów)
2. Uruchom moduły (parsowanie)
3. Analizuj wyniki (CSV/JSON)
4. Koreluj dane między artefaktami

---

## Wskazówki praktyczne

- używaj gotowych kolekcji (`!BasicCollection`)
- analizuj wyniki w Excelu lub Timeline Explorer
- filtruj po czasie i nazwach plików
- łącz dane z różnych źródeł (Prefetch, Registry, Recycle Bin)

---