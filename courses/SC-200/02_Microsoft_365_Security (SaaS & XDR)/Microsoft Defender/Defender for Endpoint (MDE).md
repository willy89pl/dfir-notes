---
tags:
  - sc-200
  - microsoft
  - defender
  - xdr
  - security
category: sc-200
---


# Microsoft Defender for Endpoint (MDE)

Microsoft Defender for Endpoint (MDE) to chmurowa platforma bezpieczeństwa urządzeń końcowych (Enterprise Endpoint Security). Odpowiada za wykrywanie zaawansowanych ataków, automatyczne blokowanie zagrożeń oraz dostarczanie narzędzi typu EDR (Endpoint Detection and Response) do aktywnego polowania na cyberzagrożenia.

---

## Kluczowe Komponenty Architektury MDE

Podczas egzaminu SC-200 system testuje znajomość następujących warstw ochrony:

* **Next-Generation Protection (NGP):** Klasyczny, ale oparty na chmurze antywirus (Microsoft Defender Antivirus). Blokuje znane złośliwe oprogramowanie (malware) w czasie rzeczywistym za pomocą sygnatur i heurystyki.
* **Endpoint Detection and Response (EDR):** Sercem SOC. Rejestruje zachowania systemowe, anomalie procesów, modyfikacje rejestru i aktywność sieciową na urządzeniach. Pozwala analitykom na izolację maszyn lub zabijanie procesów.
* **Attack Surface Reduction (ASR):** Zestaw reguł zmniejszających podatność systemu na infekcję (np. blokowanie uruchamiania skryptów przez aplikacje pakietu Office, blokowanie wstrzykiwania kodu do pamięci).
* **Automated Investigation and Remediation (AIR):** Automatyczne badanie incydentów przez wbudowany silnik sztucznej inteligencji. System sam analizuje alert, buduje graf ataku i może automatycznie usunąć złośliwy plik z kwarantanny.

## Dochodzenie w SOC (Perspektywa SC-200)

Głównym źródłem danych z urządzeń w Microsoft Sentinel są tabele:
* `DeviceProcessEvents`: Rejestracja tworzenia i uruchamiania procesów systemowych.
* `DeviceNetworkEvents`: Logi wszystkich połączeń sieciowych inicjowanych przez urządzenia.
* `DeviceFileEvents`: Modyfikacje, tworzenie i usuwanie plików na dyskach.
* `DeviceRegistryEvents`: Wszelkie modyfikacje kluczy rejestru systemowego Windows.

> [!CAUTION] Krytyczna akcja pod egzamin
> W przypadku wykrycia aktywnego ataku (np. Ransomware), pierwszą i najważniejszą akcją analityka SOC w portalu Defender jest **Isolate Device** (Izolacja urządzenia). Odcina to maszynę od całej sieci wewnętrznej oraz internetu, pozostawiając jedynie szyfrowany tunel do chmury Microsoft w celu prowadzenia dalszych analiz.