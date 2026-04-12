---
tags:
  - tool
  - forensics
  - windows
  - registry
  - zimmermantools
  - hive
category: tools
platform: windows
---

# Registry Explorer

Narzędzie Erica Zimmermana do przeglądania plików hive rejestru Windows offline — zarówno z żywego systemu jak i z artefaktów KAPE.

---

## Obsługiwane pliki hive

| Plik | Lokalizacja | Zawartość |
|------|-------------|-----------|
| `SYSTEM` | `C:\Windows\System32\config\` | Konfiguracja systemu, usługi |
| `SOFTWARE` | `C:\Windows\System32\config\` | Zainstalowane aplikacje, ustawienia |
| `SAM` | `C:\Windows\System32\config\` | Konta lokalne |
| `SECURITY` | `C:\Windows\System32\config\` | Polityki bezpieczeństwa |
| `NTUSER.DAT` | `C:\Users\<user>\` | Ustawienia użytkownika |
| `UsrClass.dat` | `C:\Users\<user>\AppData\Local\Microsoft\Windows\` | Shell i COM ustawienia |

---

## Użycie

Aplikacja GUI — otwierasz plik hive przez **File → Load hive** i przeglądasz strukturę drzewa.

Wspiera też **bookmarks** dla popularnych kluczy forensycznych — wbudowana lista ważnych lokalizacji w rejestrze.

---

## Przydatne ścieżki forensyczne

```
# Assigned Access (Kiosk Mode)
SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon

# Autorun
SOFTWARE\Microsoft\Windows\CurrentVersion\Run

# Ostatnio uruchamiane programy
NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
```

---

## Gdzie pobrać

Część pakietu **Eric Zimmerman Tools**:
```
https://ericzimmerman.github.io/
```
