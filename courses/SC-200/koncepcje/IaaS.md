---
tags:
  - sc-200
  - microsoft
  - cloud
  - iaas
category: sc-200
---

# IaaS (Infrastructure as a Service) — W skrócie

**IaaS (Infrastruktura jako usługa)** to najbardziej elastyczny model chmurowy, w którym kupujesz od dostawcy "czysty sprzęt" wirtualny: moc obliczeniową (procesory, RAM), dyski oraz sieć. Dostajesz pustą maszynę, na której sam musisz zainstalować system operacyjny i wszystko skonfigurować od zera.



### Przykłady w Azure
* **Azure Virtual Machines (VMs)** – wirtualne serwery Windows / Linux
* **Azure Virtual Network (VNet)** – wirtualne sieci, podsieci
* **Azure Disk Storage** – wirtualne dyski twarde podpinane do maszyn

---

## Perspektywa SC-200 & Bezpieczeństwo

W modelu IaaS **odpowiedzialność klienta za bezpieczeństwo jest NAJWIĘKSZA** spośród wszystkich modeli chmurowych.

* **Za co odpowiada Microsoft:** Wyłącznie za fizyczną infrastrukturę (serwerownie, prąd, chłodzenie) oraz warstwę wirtualizacji (hypervisor).
* **Za co odpowiadasz TY (Klient):** 1. **System Operacyjny (OS):** Sam musisz dbać o instalowanie poprawek bezpieczeństwa (Patch Management) i antywirusa.
	2. **Konfiguracja sieci:** Musisz sam skonfigurować zapory sieciowe (np. *Network Security Groups - NSG*), aby zablokować ruch z internetu.
	3. **Aplikacje i dane:** Wszystko, co zainstalujesz na tej maszynie, jest Twoją odpowiedzialnością.

> [!IMPORTANT] Zapamiętaj pod egzamin
> Maszyny wirtualne (IaaS) są podatne na dokładnie takie same zagrożenia jak zwykłe serwery w biurze (malware, ransomware, ataki brute-force na porty SSH/RDP). Pod egzamin SC-200 zapamiętaj, że do ich ochrony używa się **Microsoft Defender for Servers** (część Defender for Cloud), który instaluje na systemie operacyjnym maszyn agenta **Microsoft Defender for Endpoint (MDE)**.