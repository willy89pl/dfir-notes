---
tags:
  - sc-200
  - microsoft
  - defender
  - office365
  - emailsecurity
category: sc-200
---
---
tags:
  - SC-200
  - Defender
  - Office365
  - EmailSecurity
---
# Microsoft Defender for Office 365 (MDO)

Microsoft Defender for Office 365 (**MDO**) to system ochrony poczty elektronicznej oraz narzędzi do pracy wspólnej (**Teams, Sharepoint, OneDrive**). Zabezpiecza organizację przed zaawansowanymi atakami phishingowymi, złośliwymi linkami (URL), zainfekowanymi załącznikami oraz próbami podszywania się pod kadrę zarządzającą (Business Email Compromise - BEC).

---

## Dwa Poziomy Ochrony (Plan 1 vs Plan 2)

Egzamin SC-200 wymaga rozróżnienia możliwości technicznych poszczególnych wersji:

### 1. Zapobieganie i Wykrywanie (MDO Plan 1)
Skupia się na blokowaniu zagrożeń "na wejściu" za pomocą flagowych technologii:
* **Safe Links (Bezpieczne linki):** Skanuje i weryfikuje każdy adres URL w mailu lub pliku Office w momencie kliknięcia przez użytkownika (Time-of-click protection). Jeśli link prowadzi do złośliwej strony, zostaje zablokowany.
* **Safe Attachments (Bezpieczne załączniki):** Otwiera każdy podejrzany załącznik w odizolowanym środowisku wirtualnym chmury (Sandboxing). Dopiero po upewnieniu się, że plik nie wykonuje złośliwego kodu, trafia on do skrzynki użytkownika.

### 2. Dochodzenie i Edukacja (MDO Plan 2)
Dedykowany dla zespołów SOC:
* **Threat Explorer:** Narzędzie do głębokiej analizy kampanii phishingowych trafiających do organizacji. Pozwala na masowe usuwanie złośliwych wiadomości ze skrzynek wszystkich użytkowników (*ZAP - Zero-hour Auto Purge*).
* **Attack Simulation Training:** Moduł do przeprowadzania kontrolowanych, próbnych testów phishingowych wśród pracowników.

## Tabela Logów w Microsoft Sentinel

Wszystkie zdarzenia związane z przepływem poczty analizujemy w Sentinel za pomocą następujących tabel KQL:

| Tabela                | Co zawiera?                                                                   | Cel analizy                                                                     |
| :-------------------- | :---------------------------------------------------------------------------- | :------------------------------------------------------------------------------ |
| `EmailEvents`         | Dane nagłówkowe maili (nadawca, odbiorca, IP nadawcy, temat, decyzja filtra). | Wykrywanie masowych kampanii SPAM/Phishing.                                     |
| `EmailUrlInfo`        | Wykaz wszystkich linków URL zawartych w odebranych wiadomościach.             | Wyszukiwanie wskaźników kompromitacji (IoC) powiązanych ze złośliwymi domenami. |
| `EmailAttachmentInfo` | Szczegóły dotyczące załączników (nazwa pliku, rozszerzenie, hash SHA256).     | Identyfikacja złośliwych makr i plików wykonywalnych.                           |