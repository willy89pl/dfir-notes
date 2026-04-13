---
tags: [windows, artifact, forensics, ntfs, filesystem]
category: windows
platform: windows
---

# $LogFile

Dziennik transakcji systemu plików NTFS — rejestruje operacje na plikach zanim zostaną zatwierdzone. Służy głównie do odtworzenia spójności systemu plików po awarii, ale jest też cennym artefaktem forensycznym.

> Pełny obraz operacji na plikach daje dopiero trio: **[[mft]] + [[logfile]] + [[usnjrnl]]**

---

## Lokalizacja

```
C:\$LogFile
```

---

## Czym różni się od $MFT i $UsnJrnl

| Artefakt | Co rejestruje |
|----------|--------------|
| **$MFT** | Stan końcowy plików |
| **$LogFile** | Dziennik transakcji NTFS — operacje przed zatwierdzeniem |
| **$UsnJrnl** | Chronologiczny dziennik wszystkich zmian |

---

## Ograniczenia

- Nadpisywany **cyklicznie** — przechowuje tylko ostatnie kilkadziesiąt MB operacji
- Trudniejszy w analizie niż $MFT czy $UsnJrnl
- Wymaga specjalistycznych narzędzi do parsowania

---

## Jak przeglądać

Brak narzędzia od Zimmermana — używa się:

- **[[ntfs-log-tracker]]** — parsuje $LogFile razem z [[$MFT]] i [[$UsnJrnl]]
- **LogFileParser** — open source, dostępny na GitHub

---

## Kiedy sięgać po $LogFile

Gdy $MFT i $UsnJrnl nie dają wystarczających informacji — np. przy analizie bardzo niedawnych operacji lub gdy potrzebujesz potwierdzenia transakcji na poziomie systemu plików.
