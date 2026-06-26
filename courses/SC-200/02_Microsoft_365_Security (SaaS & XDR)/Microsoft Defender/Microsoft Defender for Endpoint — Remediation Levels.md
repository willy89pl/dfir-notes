---
tags:
  - sc-200
  - microsoft
  - defender
  - endpoint
  - remediation
category: sc-200
---

# Microsoft Defender for Endpoint — Remediation Levels

Podczas konfiguracji grup urządzeń (Device Groups) w portalu Microsoft Defender XDR, jednym z kluczowych ustawień jest określenie poziomu automatyzacji działań naprawczych (Remediation Level). Ustawienie to definiuje, jak głęboko silnik Automated Investigation and Remediation (AIR) może ingerować w system operacyjny w przypadku wykrycia zagrożenia (np. pliku ransomware czy złośliwego procesu) bez bezpośredniego udziału analityka SOC.

---

## Poziomy Automatyzacji i ich Charakterystyka

Poniższa tabela przedstawia dostępne opcje konfiguracji, ich techniczny opis oraz rekomendowane scenariusze użycia (Use Cases):

| Opcja                                                    | Opis                                                                                                                            | Use Case                                                                        |
| :------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------ |
| **Full - remediate threats automatically**               | Defender automatycznie dokonuje analizy i wykonuje akcje na wszystkich zasobach. <br>Zatwierdzenie nie jest potrzebne.          | Najlepsze dla ogólnego zastosowania , szczególnie gdzie liczy się czas reakcji. |
| **Semi - require approval for any remediation**          | Jak wyżej, ale z wyjątkiem krytycznych folderów systemowych (np. C:\windows ; c:\program files)<br>Zatwierdzenie jest wymagane. | Bezpieczniejsza automatyzacja i nadal ochrona kluczowych obszarów systemu.      |
| **Semi - require approval for non-folder remediation**   | Defender może automatycznie reagować/kwarantannować zagrożenia w folderach tymczasowych. Potwierdzenie jest wymagane.           | Podejście zbalansowane dla stacji końcowych.                                    |
| **Semi - require approval for core folders remediation** | Defender będzie rekomendował działania, ale trzeba wszystko ręcznie zatwierdzać, nawet foldery tymczasowe.                      | Podejście w którym jest pełna kontrola nad reakcją Defendera.                   |
| **No automatic remediation**                             | Defender wykrywa zagrożenia, loguje je. Nie podejmuje żadnej akcji.                                                             | Zastosowanie dla bradzo wrażliwych systemów - serwerów, DC itp.                 |

---

## Perspektywa SC-200 i SOC

Zarządzanie zatwierdzeniami z poziomów *Semi* odbywa się z poziomu zunifikowanego portalu Defender w zakładce **Actions & submissions** ➔ **Action Center**.

> [!TIP] Złota zasada pod egzamin:
> Jeśli w pytaniu scenariuszowym pojawia się wymóg, że **zagrożenia na serwerach produkcyjnych muszą być badane automatycznie, ale usuwane dopiero po weryfikacji przez administratora**, prawidłową odpowiedzią jest wybór jednego z poziomów **Semi (Require approval)**. Dla stacji roboczych standardem w strategii Zero Trust jest poziom **Full**.