---
tags:
  - tool
  - sqlite
category: tools
---


 Sztuczka w DB Browser na zamienianie czasu z Windows(FILETIME) na czytelny dla człowieka.
> ```SELECT datetime(13405252103670523/1000000 - 11644473600, 'unixepoch')
> ```
> czyli FILETIME dzielimy przez milion i odejmujemy stałą wartość 11644473600 (róznica między FILETIME a UNIXTIME) i zamieniamy funkcją unixepoch
> Logia jest ta sama i można implementować w innych programach i językach