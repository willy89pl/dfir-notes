---
tags:
  - powershell
  - forensics
  - windows
  - artifact
  - ps
  - history
category: windows
platform: windows
---

# PSReadline

Moduł PowerShell odpowiedzialny za obsługę linii poleceń w konsoli — autouzupełnianie, kolorowanie składni i historia poleceń. Odpowiednik `readline` na Linuxie.

---

## Dostępność

Domyślnie włączony od **Windows 10** i **PowerShell 5.1** — nie wymaga żadnej konfiguracji.

---

## Lokalizacja artefaktu

```
C:\Users\<user>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

Domyślnie przechowuje ostatnie **4096 poleceń** i kumuluje je między sesjami.

---

## Znaczenie dla forensics

Jeden z cenniejszych artefaktów przy analizie działań atakującego:

- Zapisuje komendy nawet jeśli **PowerShell logging nie był włączony**
- Persystuje między sesjami — komendy się kumulują
- Atakujący często o nim zapominają i nie czyszczą

---

## Ograniczenia

- Rejestruje tylko komendy wpisane **interaktywnie** — skrypty uruchamiane przez `.\skrypt.ps1` nie zostawiają śladów poszczególnych linii
- Można wyłączyć: `Set-PSReadlineOption -HistorySaveStyle SaveNothing`
- Można wyczyścić ręcznie przez usunięcie pliku lub `Clear-History`

---

## Jak przeglądać

Zwykły plik tekstowy — otwierasz bezpośrednio w dowolnym edytorze.
