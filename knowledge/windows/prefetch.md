---
tags: [windows, artifact, forensics, execution]
category: windows
platform: windows
---

# Prefetch

Mechanizm Windows przyspieszający uruchamianie aplikacji — system zapisuje informacje o plikach używanych podczas startu programu żeby kolejne uruchomienie było szybsze. Przy okazji zostawia cenne ślady do analizy.

---

## Lokalizacja

```
C:\Windows\Prefetch\
```

Pliki mają format `NAZWAAPLIKACJI-HASH.pf`, np.:
```
MSEDGE.EXE-A1B2C3D4.pf
CMD.EXE-F1E2D3C4.pf
```

---

## Co rejestruje

- **Nazwa pliku** wykonywalnego
- **Ścieżka** z której był uruchomiony
- **Czas ostatniego uruchomienia**
- **Liczba uruchomień**
- **Do 8 ostatnich czasów uruchomień** (Windows 8+)
- Lista plików i folderów używanych podczas uruchomienia

---

## Znaczenie dla analizy

- Potwierdza **czy i kiedy** program był uruchomiony
- Różne hashe dla tego samego pliku z **różnych ścieżek** — np. `cmd.exe` z System32 i `cmd.exe` z Downloads będą miały osobne pliki .pf
- Przydatny do wykrycia **renamed executables** — nazwa w pliku .pf odpowiada nazwie w momencie uruchomienia

---

## Ograniczenia

- Domyślnie **wyłączony na Windows Server**
- Przechowuje maksymalnie **128 ostatnich plików** (Windows 10)
- Rejestruje tylko **pierwsze 8 czasów uruchomień** — starsze są nadpisywane

---

## Jak przeglądać

[[pecmd]] (Eric Zimmerman):
```bash
PECmd.exe -d "C:\Windows\Prefetch" --csv output_folder
```

Następnie przeglądanie w [[timeline-explorer]]
