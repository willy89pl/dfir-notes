---
tags:
  - sc-200
  - microsoft
  - cloud
  - casb
  - mdca
  - security
category: sc-200
---


# CASB (Cloud Access Security Broker) — Charakterystyka (Pod SC-200)

**CASB (Pośrednik bezpieczeństwa dostępu do chmury)** to punkt wymuszania polityk bezpieczeństwa (on-premises lub chmurowy), umieszczony między użytkownikami przedsiębiorstwa a dostawcami usług chmurowych. Działa jak "strażnik" lub "filtr", który monitoruje cały ruch zmierzający do i z chmury, dbając o to, aby ruch ten był zgodny z polityką bezpieczeństwa firmy.

W ekosystemie Microsoft rolę pełnoprawnego CASB pełni **Microsoft Defender for Cloud Apps (MDCA)**.


---

## Cztery Filary CASB

Aby dobrze odpowiedzieć na pytania scenariuszowe na egzaminie SC-200, musisz znać cztery główne filary, na których opiera się technologia CASB:

### 1. Widoczność (Visibility) i walka z Shadow IT
* **Problem:** Pracownicy masowo korzystają z nieautoryzowanych przez dział IT aplikacji chmurowych (np. wrzucają pliki firmowe na prywatny Dropbox/WeTransfer, bo tak im wygodniej). To zjawisko nazywamy **Shadow IT**.
* **Rozwiązanie CASB:** Narzędzie analizuje logi z firewalli sieciowych i proxy, a następnie pokazuje administratorowi pełną listę aplikacji chmurowych, z których korzystają ludzie w firmie. CASB od Microsoftu (MDCA) ma bazę ponad 31 000 aplikacji, z których każda ma przypisaną ocenę ryzyka (Sanctioned vs Unsanctioned).

### 2. Bezpieczeństwo Danych (Data Security) i ochrona przed wyciekiem (DLP)
* CASB potrafi skanować pliki przechowywane w chmurach (nie tylko OneDrive, ale też AWS S3 czy Google Drive) w poszukiwaniu wrażliwych danych, takich jak numery kart kredytowych, PESEL, czy dokumenty oznaczone jako poufne.
* Pozwala na blokowanie akcji w czasie rzeczywistym – np. uniemożliwia pobranie pliku z etykietą "ściśle tajne", jeśli pracownik loguje się z prywatnego, niezarządzanego przez firmę komputera.

### 3. Ochrona przed Zagrożeniami (Threat Protection)
* Analizuje zachowanie użytkowników w chmurze (UEBA – User and Entity Behavior Analytics) i wykrywa anomalie.
* *Przykłady wykrywanych anomalii:* Ten sam użytkownik nagle pobiera 50 GB danych z SharePointa w 3 minuty (potencjalna kradzież danych przed odejściem z pracy) albo loguje się do systemu Salesforce z Nigerii, podczas gdy godzinę wcześniej pracował z Warszawy (*Impossible Travel*).

### 4. Zgodność z przepisami (Compliance)
* CASB pomaga zweryfikować, czy używane w firmie aplikacje chmurowe spełniają wymogi prawne i regulacyjne (np. RODO/GDPR, HIPAA, ISO 27001). Możesz łatwo wygenerować raport pokazujący, które aplikacje używane przez pracowników nie szyfrują danych w spoczynku.

---

## Krytyczny Punkt Egzaminacyjny: Jak CASB wdrożyć w praktyce?

Na egzaminie SC-200 spotkasz pytania o to, jak technicznie zmusić ruch, aby przechodził przez CASB (MDCA). Microsoft realizuje to na dwa sposoby:

1. **Integracja przez API (App Connectors):** * MDCA łączy się bezpośrednio z backendem danej aplikacji (np. Microsoft 365, Salesforce, AWS, ServiceNow) za pomocą tokenów API. 
   * *Zaleta:* Pozwala skanować pliki, które już tam leżą (w spoczynku) oraz monitorować aktywność, nawet gdy użytkownik loguje się z domowego komputera.
2. **Kontrola w czasie rzeczywistym (Conditional Access App Control):**
   * Wykorzystuje integrację z **Dostępem Warunkowym (Entra ID)** jako odwrotne proxy (*Reverse Proxy*). 
   * Gdy użytkownik loguje się do aplikacji, Dostęp Warunkowy nie wpuszcza go tam bezpośrednio, ale przekierowuje jego sesję przez serwery proxy MDCA. Dzięki temu każda próba kliknięcia "Pobierz" czy "Udostępnij" jest sprawdzana na żywo.

---

## Co monitorujesz w SOC (Logi z CASB w Sentinel)?

Logi z Microsoft Defender for Cloud Apps są przesyłane do **Microsoft Sentinel** poprzez natywny konektor. Szukasz w nich:
* **Cloud Discovery Logs:** Raporty o nowo wykrytych, ryzykownych aplikacjach chmurowych w sieci.
* **Activity Logs:** Ślady poruszania się intruza wewnątrz aplikacji SaaS (np. zmiana uprawnień administracyjnych w Salesforce przez skradzione konto).
* **Alerts:** Gotowe alarmy o masowym usuwaniu plików, ransomware w chmurze czy podejrzeniach przejęcia sesji przeglądarki (Session Hijacking).

> [!TIP] Złota zasada pod egzamin:
> Kiedy w pytaniu scenariuszowym pojawia się problem **Shadow IT**, **monitorowania zewnętrznych chmur (AWS/SaaS)** lub **blokowania pobierania plików na prywatne komputery w czasie rzeczywistym** — właściwą odpowiedzią niemal zawsze jest rozwiązanie klasy CASB, czyli **Microsoft Defender for Cloud Apps (MDCA)**.