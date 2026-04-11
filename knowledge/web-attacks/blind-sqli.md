---
tags: [web-attack, sqli, blind-sqli]
category: web-attacks
---

# Blind SQL Injection (Blind SQLi)

Rodzaj ataku SQL Injection, w którym aplikacja jest podatna na wstrzyknięcie kodu, ale jej odpowiedzi **nie zawierają bezpośrednich wyników zapytania** ani komunikatów o błędach bazy danych.

W przeciwieństwie do klasycznego SQLi (gdzie dane pojawiają się wprost na stronie), w wersji "ślepej" atakujący zadaje bazie pytania **tak/nie** i obserwuje reakcję serwera.

---

## Główne rodzaje

**Boolean-based (oparty na logice)**
Atakujący wysyła zapytania sprawdzające prawdziwość warunku. Jeśli strona ładuje się normalnie — warunek prawdziwy. Jeśli treść się zmienia lub znika — fałszywy.

**Time-based (oparty na czasie)**
Wykorzystuje funkcje wstrzymujące działanie bazy danych (np. `SLEEP()`). Jeśli serwer odpowiada z opóźnieniem — wstrzyknięty warunek został spełniony.

---

## Jak sqlmap realizuje Boolean-based Blind SQLi

Mechanizm użyty w labie [[perfect-survey-lab]]:

```
MID(user_login, N, 1)  — wycięcie znaku na pozycji N
ORD(...)               — zamiana znaku na kod ASCII
binary search przez porównania > X
200 = TAK / 404 = NIE  — jedyna informacja jaką daje serwer
```

Sqlmap najpierw pyta o liczbę znaków w wartości, potem odgaduje kolejne znaki przez binary search na kodach ASCII — aż odtworzy pełną wartość pola.

---

## Narzędzia

| Narzędzie | Opis |
|-----------|------|
| **sqlmap** | Automatyczna eksploitacja SQLi |
| **Burp Suite** | Ręczna analiza zapytań |

---

## Detekcja w Splunk

```spl
index=* source="access.log"
| eval decoded = urldecode(uri_query)
| search decoded="*CAST*" OR decoded="*SLEEP*" OR decoded="*ORD(*"
| table _time, clientip, status, decoded
| sort by _time asc
```

Charakterystyczne wzorce w logach:
- Duża liczba podobnych zapytań z tego samego IP
- Zapytania zawierające `CAST`, `ORD`, `MID`, `LIMIT X,1`
- Odpowiedzi naprzemiennie 200/404 dla bardzo podobnych URI
