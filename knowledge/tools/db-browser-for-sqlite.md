---
tags: [tool, forensics, sqlite, browser-forensics, database]
category: tools
platform: windows/linux/mac
---

# DB Browser for SQLite

Graficzne narzędzie do przeglądania i odpytywania baz danych SQLite — przydatne w forensic do analizy artefaktów przeglądarek (historia, pobrane pliki, ciasteczka).

---

## Gdzie pobrać

```
https://sqlitebrowser.org/
```

---

## Typowe artefakty SQLite w forensic

| Plik | Lokalizacja | Zawartość |
|------|-------------|-----------|
| `History` | `C:\Users\<user>\AppData\Local\Microsoft\Edge\User Data\Default\` | Historia przeglądania Edge |
| `History` | `C:\Users\<user>\AppData\Local\Google\Chrome\User Data\Default\` | Historia przeglądania Chrome |
| `Downloads` | Jak wyżej | Historia pobranych plików |
| `Cookies` | Jak wyżej | Ciasteczka |
| `Login Data` | Jak wyżej | Zapisane hasła |

---

## Kluczowe tabele w pliku History (Edge/Chrome)

| Tabela | Zawartość |
|--------|-----------|
| `urls` | Lista odwiedzonych URLi z tytułami |
| `visits` | Każda wizyta z timestampem i typem przejścia |
| `downloads` | Historia pobranych plików |
| `keyword_search_terms` | Historia wyszukiwań |

---

## Przydatne zapytania SQL

**Pełna historia z timestampem:**
```sql
SELECT urls.url, urls.title, visits.visit_time, visits.transition
FROM visits
JOIN urls ON visits.url = urls.id
ORDER BY visit_time DESC
```

**Przeliczenie timestampu Chrome/Edge na UTC:**
```sql
SELECT datetime(visit_time/1000000 - 11644473600, 'unixepoch')
FROM visits
```

---

## Format timestampów Chrome/Edge

Chrome i Edge używają **WebKit timestamp** — mikrosekundy od **1 stycznia 1601** (epoka Windows).

Żeby przeliczyć na Unix timestamp (epoka 1970) odejmuje się stałą **11644473600 sekund** — różnicę między epokami.

```sql
datetime(timestamp/1000000 - 11644473600, 'unixepoch')
```

---

## Kolumna transition w tabeli visits

Określa **jak** URL został otwarty — przydatna do ustalenia czy URL był wywołany przez system, kliknięcie linka czy wpisany ręcznie. Wartość jest bitmaskową liczbą dziesiętną.


## Sztuczka , opisane powyżej
 Sztuczka w DB Browser na zamienianie czasu z Windows(FILETIME) na czytelny dla człowieka.
> ```SELECT datetime(13405252103670523/1000000 - 11644473600, 'unixepoch')
> ```
> czyli FILETIME dzielimy przez milion i odejmujemy stałą wartość 11644473600 (róznica między FILETIME a UNIXTIME) i zamieniamy funkcją unixepoch
> Logia jest ta sama i można implementować w innych programach i językach