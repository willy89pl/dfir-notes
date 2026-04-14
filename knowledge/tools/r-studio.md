---
tags:
  - tool
  - forensic
  - windows
  - ntfs
  - filesystem
  - mft
category: tools
platform: windows
---

# R-Studio w Informatyce Śledczej (Digital Forensics)

## Opis ogólny
**R-Studio** to zaawansowane narzędzie do odzyskiwania danych i analizy systemów plików. Choć często kojarzone z komercyjnym ratowaniem plików, jest powszechnie cenione w informatyce śledczej ze względu na potężny silnik rekonstrukcji struktur systemów plików (zwłaszcza NTFS, ReFS, APFS i ext4).

## Kluczowe funkcje w kontekście Forensic
* **Analiza MFT (Master File Table):** Program potrafi czytać i interpretować rekordy MFT, pozwalając na odnalezienie plików usuniętych, nawet jeśli ich wpisy w tabeli zostały oznaczone jako wolne.
* **Obsługa obrazów dysków:** Umożliwia montowanie i analizę obrazów bitowych (np. plików `.bin`, `.img`, `.rdr`) oraz obrazów stworzonych przez inne narzędzia forensic.
* **Podgląd niskopoziomowy (Hex Editor):** Posiada wbudowany edytor szesnastkowy, który pozwala na ręczną weryfikację struktur plików, nagłówków oraz analizę *slack space*.
* **Odzyskiwanie po sygnaturach (Carving):** Jeśli system plików jest całkowicie zniszczony, R-Studio przeszukuje dysk w poszukiwaniu znanych nagłówków plików (np. JPG, PDF, DOCX).

## Analiza artefaktów NTFS
R-Studio pozwala na szczegółowy wgląd w:
1.  **Resident Files:** Identyfikacja małych plików przechowywanych bezpośrednio w rekordzie [[mft]].
2.  **Alternate Data Streams (ADS):** Łatwy wgląd w dodatkowe strumienie danych (np. Zone.Identifier).
3.  **Znaczniki czasu:** Wyświetlanie dat modyfikacji, dostępu i utworzenia pobranych prosto z atrybutów `$STANDARD_INFORMATION` oraz `$FILE_NAME`.

## Zalety
* Wysoka skuteczność w odnajdywaniu partycji po formatowaniu.
* Stabilna praca na uszkodzonych fizycznie nośnikach (dobra obsługa błędów odczytu).
* Możliwość analizy przez sieć.
