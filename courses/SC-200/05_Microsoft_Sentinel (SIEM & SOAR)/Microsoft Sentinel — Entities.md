---
tags:
  - sc-200
  - microsoft
  - sentinel
  - investigation
  - entities
category: sc-200
---

# Microsoft Sentinel - Entities (Encje)

**Entities (Encje)** to kluczowe i unikalne obiekty tożsamości, infrastruktury lub danych, które biorą udział w incydencie bezpieczeństwa. Microsoft Sentinel automatycznie rozpoznaje i wyciąga je z surowych linii logów, aby analityk SOC nie musiał ręcznie szukać nazw komputerów czy adresów IP w tekście.

Encje stanowią fundament pod działanie **Grafu Dochodzeniowego (Investigation Graph)** oraz automatyzacji w **Playbookach**.

---

## Najważniejsze Typy Encji (Matryca SC-200)

Sentinel rozpoznaje kilkanaście wbudowanych typów encji. Do najważniejszych należą:

* **Account (Konto):** Nazwa użytkownika, identyfikator SID lub konto e-mail (np. `jan.kowalski@firma.pl`).
* **Host (Urządzenie):** Nazwa komputera, serwera lub jego unikalny identyfikator w chmurze (np. `SRV-PROD-01`).
* **IP (Adres IP):** Adres IPv4 lub IPv6 (np. `185.23.44.112`).
* **File (Plik):** Nazwa pliku oraz jego ścieżka.
* **FileHash:** Unikalny skrót kryptograficzny pliku (SHA-256, MD5) pozwalający jednoznacznie zidentyfikować malware.
* **URL / Domain:** Adres strony internetowej lub domena, z którą łączyło się złośliwe oprogramowanie.
* **Process:** Uruchomiony w systemie proces (np. `powershell.exe` z konkretnymi argumentami).

---

## Dlaczego Mapowanie Encji (Entity Mapping) jest kluczowe?

Podczas tworzenia własnych reguł analitycznych (**Analytics Rules**) w KQL, musisz na końcu zapytania wykonać tzw. **Entity Mapping** (Mapowanie encji). Wskazujesz wtedy Sentinelowi, które kolumny z Twoich logów odpowiadają rzeczywistym encjom.

### Przykład:
Jeśli piszesz regułę szukającą ataków w tabeli `SigninLogs`, musisz w kreatorze kliknąć:
* Mapuj kolumnę `IPAddress` ➔ jako encję typu **IP**.
* Mapuj kolumnę `UserPrincipalName` ➔ jako encję typu **Account**.

### Korzyści z mapowania encji w SOC:
1. **Wizualizacja (Graf):** Sentinel połączy te kropki na wykresie. Zobaczysz ikonę komputera połączoną czerwoną linią z ikoną użytkownika i adresu IP.
2. **Korelacja (Fusion):** Silnik Machine Learning wie, że ten sam adres IP, który atakował serwer (logi z zapory), sekundę później logował się do Office 365 (logi Entra ID). Dzięki temu Sentinel połączy dwa różne alerty w jeden incydent.
3. **Paliwo dla SOAR:** Playbooki potrzebują encji jako parametrów wejściowych. Przykładowo, playbook *"Izoluj hosta"* zadziała tylko wtedy, gdy z incydentu dostanie prawidłowo zmapowaną encję typu **Host**.

---

> [!TIP] Entity Pages (Strony encji)
> W lewym menu Sentinela znajdziesz zakładkę **Entity behavior** (UEBA). Możesz tam wpisać nazwę dowolnego pracownika lub hosta, aby otworzyć jego dedykowaną stronę (Entity Page). Znajdziesz tam oś czasu wszystkich jego działań, listę alertów z nim powiązanych oraz ocenę poziomu ryzyka wyliczoną przez algorytmy AI Microsoftu.