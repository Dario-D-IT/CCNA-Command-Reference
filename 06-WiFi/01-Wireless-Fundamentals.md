# 01 - Osnove bežičnog umrežavanja

## Svrha
Bežične mreže koriste **radio valove** za prijenos podataka umjesto fizičkih kabela. Razumijevanje standarda, frekvencija i topologija temelj je za konfiguraciju i troubleshooting WiFi mreža.

---

## OSI sloj
Bežično umrežavanje radi na **Physical (Layer 1)** i **Data Link Layeru (Layer 2)**.

---

## 1. 802.11 standardi

| Standard | Frekvencija | Maks. brzina | Napomena |
| :--- | :--- | :--- | :--- |
| **802.11a** | 5 GHz | 54 Mbps | Stariji, manji domet |
| **802.11b** | 2.4 GHz | 11 Mbps | Zastarjelo |
| **802.11g** | 2.4 GHz | 54 Mbps | Kompatibilan s b |
| **802.11n (WiFi 4)** | 2.4 i 5 GHz | 600 Mbps | MIMO antene |
| **802.11ac (WiFi 5)** | 5 GHz | 3.5 Gbps | MU-MIMO, beamforming |
| **802.11ax (WiFi 6)** | 2.4 i 5 GHz | 9.6 Gbps | OFDMA, najefikasniji |

---

## 2. Frekvencije: 2.4 GHz vs 5 GHz

| Karakteristika | 2.4 GHz | 5 GHz |
| :--- | :--- | :--- |
| Domet | Veći | Manji |
| Brzina | Manja | Veća |
| Interferencija | Veća (mikrovalni, Bluetooth) | Manja |
| Prolaz kroz zidove | Bolji | Lošiji |
| Broj nepreklapajućih kanala | **3** (1, 6, 11) | **25+** |

---

## 3. Kanali

### 2.4 GHz — samo 3 nepreklapajuća kanala
```
Kanal 1:   2.412 GHz
Kanal 6:   2.437 GHz
Kanal 11:  2.462 GHz
```
> Susjedni AP-ovi trebaju koristiti kanale **1, 6 ili 11** kako bi se izbjegla interferencija.

### 5 GHz — 25+ nepreklapajućih kanala
Puno više prostora → manja interferencija → preporučeno za gustije sredine.

---

## 4. Topologije bežičnih mreža

| Naziv | Opis |
| :--- | :--- |
| **IBSS** (Ad-hoc) | Direktna veza između dva uređaja, bez AP-a |
| **BSS** (Infrastructure) | Uređaji se spajaju putem jednog AP-a |
| **ESS** (Extended Service Set) | Više AP-ova s istim SSID-om — roaming između njih |
| **MBSS** (Mesh) | AP-ovi se međusobno bežično povezuju |

### Pojmovi
- **SSID** — naziv bežične mreže (do 32 znaka)
- **BSSID** — MAC adresa AP-a (jedinstveni identifikator BSS-a)
- **DS** (Distribution System) — žična infrastruktura koja povezuje AP-ove

---

## 5. CSMA/CA — kako bežična mreža dijeli medij

Bežične mreže ne mogu **istovremeno slati i primati** (half-duplex) — ne mogu detektirati koliziju kao Ethernet.

Umjesto CSMA/CD, koriste **CSMA/CA (Collision Avoidance)**:
1. Uređaj sluša je li kanal slobodan
2. Čeka određeno **random backoff** vrijeme
3. Šalje podatke
4. Prima potvrdu (**ACK**) od primatelja
5. Ako ACK ne stigne → pretpostavlja koliziju → čeka i pokušava ponovo

---

## 6. Tipovi pristupnih točaka (AP)

| Tip | Opis | Upravljanje |
| :--- | :--- | :--- |
| **Autonomous AP** | Svaki AP se konfigurira zasebno | Direktno na AP-u (CLI/GUI) |
| **Lightweight AP (LAP)** | AP samo prosljeđuje promet, logika je na WLC-u | Centralizirano putem WLC-a |

> **Split-MAC arhitektura** — kod Lightweight AP-ova, MAC funkcije su podijeljene:
> - **AP** obrađuje: slanje/primanje RF signala, enkripcija/dekripcija
> - **WLC** obrađuje: autentifikacija, upravljanje, roaming

---

## Napomene za ispit
- **2.4 GHz**: 3 nepreklapajuća kanala (1, 6, 11)
- **5 GHz**: 25+ nepreklapajućih kanala — manji interferenci
- **802.11ac (WiFi 5)** radi isključivo na **5 GHz**
- **BSS** = jedan AP, **ESS** = više AP-ova s istim SSID-om (roaming)
- Bežične mreže su uvijek **half-duplex** — koriste CSMA/CA, ne CSMA/CD
- **Autonomous AP** = standalone, **Lightweight AP** = ovisi o WLC-u
