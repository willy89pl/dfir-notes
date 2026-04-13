---
tags: [windows, artifact, forensics, powershell, event-log]
category: windows
platform: windows
---

# PowerShell Operational Log

Dziennik zdarzeń rejestrujący aktywność PowerShell na poziomie operacyjnym — uruchamianie konsoli, wykonywanie komend i skryptów.

---

## Lokalizacja

```
C:\Windows\System32\winevt\Logs\Microsoft-Windows-PowerShell%4Operational.evtx
```

---

## Kluczowe Event IDs

| Event ID | Nazwa | Co rejestruje |
|----------|-------|---------------|
| **40961** | PowerShell Console Startup | Moment uruchomienia konsoli PowerShell |
| **40962** | PowerShell Console Ready | Konsola gotowa do użycia |
| **4103** | Module Logging | Wykonanie komendy (wymaga włączonego Module Logging) |
| **4104** | Script Block Logging | Treść wykonywanego skryptu (wymaga włączonego Script Block Logging) |

---

## Znaczenie dla analizy

- **40961** potwierdza uruchomienie sesji PowerShell — przydatny do korelacji z [[prefetch]] i Security.evtx
- **4104** to goldmine jeśli jest włączony — zawiera pełną treść wykonanych skryptów
- Domyślnie 4103 i 4104 **nie są włączone** — zależą od konfiguracji audytu

---

## Ważna uwaga o timestampach

Event 40961 jest logowany ~1 sekundę **po** faktycznym uruchomieniu procesu bo rejestruje moment gdy konsola jest już gotowa, nie moment startu pliku wykonywalnego. Do precyzyjnego timestampu uruchomienia lepszy jest [[prefetch]].

---

## Jak przeglądać

**EvtxECmd** + **Timeline Explorer**:
```bash
EvtxECmd.exe -f "Microsoft-Windows-PowerShell%4Operational.evtx" --csv output_folder
```

Lub bezpośrednio w **Event Log Explorer** (GUI).
