# 02 - IPv4 Subnetting

## Svrha
Subnetting dijeli jednu veliku mrežu na manje podmreže (**subnete**) radi bolje organizacije, sigurnosti i smanjenja broadcast domena.

---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## 1. IPv4 adresa — osnove

IPv4 adresa je dugačka **32 bita**, zapisana kao **4 okteta** u decimalnom obliku:

```
192  .  168  .   1  .   1
11000000.10101000.00000001.00000001
```

### Dijelovi adrese
```
Mrežni dio   | Host dio
192.168.1    |  .1     (u /24 mreži)
```

---

## 2. Subnet maska i CIDR notacija

| CIDR | Subnet maska | Broj hosta | Korisnih hosta |
| :---: | :--- | :---: | :---: |
| /8 | 255.0.0.0 | 16.777.216 | 16.777.214 |
| /16 | 255.255.0.0 | 65.536 | 65.534 |
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |
| /31 | 255.255.255.254 | 2 | 2* |
| /32 | 255.255.255.255 | 1 | 1* |

> `/30` = Point-to-Point linkovi (2 korisna hosta)
> `/31` = Point-to-Point (RFC 3021, bez network/broadcast adrese)
> `/32` = Host route (točno jedna adresa)

**Formula:** Korisnih hosta = 2^(32-prefix) - 2

---

## 3. Klase IPv4 adresa

| Klasa | Raspon | Default maska | Namjena |
| :--- | :--- | :--- | :--- |
| **A** | 1.0.0.0 – 126.255.255.255 | /8 | Veliki ISP-ovi |
| **B** | 128.0.0.0 – 191.255.255.255 | /16 | Srednje organizacije |
| **C** | 192.0.0.0 – 223.255.255.255 | /24 | Male mreže |
| **D** | 224.0.0.0 – 239.255.255.255 | — | Multicast |
| **E** | 240.0.0.0 – 255.255.255.255 | — | Rezervirano |

### Privatne adrese (RFC 1918)
| Raspon | CIDR |
| :--- | :--- |
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

### Posebne adrese
| Adresa | Namjena |
| :--- | :--- |
| 127.0.0.1 | Loopback (localhost) |
| 169.254.x.x | APIPA (automatska, bez DHCP-a) |
| 255.255.255.255 | Limited broadcast |

---

## 4. Izračun podmreže — korak po korak

**Primjer:** Adresa `192.168.1.0/26`

```
Korak 1: Prefix → /26 = 26 jediničnih bita
          Subnet maska: 255.255.255.192

Korak 2: Broj adresa = 2^(32-26) = 2^6 = 64
          Korisnih hosta = 64 - 2 = 62

Korak 3: Network adresa  = 192.168.1.0
          Prva korisna    = 192.168.1.1
          Zadnja korisna  = 192.168.1.62
          Broadcast       = 192.168.1.63

Korak 4: Sljedeća podmreža počinje na: 192.168.1.64
64 je inkrement
```

---

## 5. Brza metoda — "Magic Number"

**Magic Number** = 256 - vrijednost oktetа gdje se maska mijenja

```
/26 → maska = 255.255.255.192
Magic Number = 256 - 192 = 64

Podmreže: 0, 64, 128, 192 (inkrementi od 64)

192.168.1.0   → Network: .0,  Broadcast: .63
192.168.1.64  → Network: .64, Broadcast: .127
192.168.1.128 → Network: .128, Broadcast: .191
192.168.1.192 → Network: .192, Broadcast: .255
```

---

## 6. VLSM — Variable Length Subnet Masking

VLSM dodjeljuje **različite veličine podmreža** prema stvarnim potrebama — nema rasipanja adresa.

**Primjer:** Adresa `192.168.1.0/24`, potrebe:
```
Odjel A: 50 hosta  → /26 (62 hosta) → 192.168.1.0/26
Odjel B: 25 hosta  → /27 (30 hosta) → 192.168.1.64/27
Odjel C: 10 hosta  → /28 (14 hosta) → 192.168.1.96/28
Link 1:   2 hosta  → /30  (2 hosta) → 192.168.1.112/30
Link 2:   2 hosta  → /30  (2 hosta) → 192.168.1.116/30
```

> Pravilo: **Počni od najveće podmreže** i idi prema manjima.

---

## 7. Binarna konverzija (brza tablica)

| Bit pozicija | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Binarno | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |

```
192 = 128 + 64         = 11000000
168 = 128 + 32 + 8     = 10101000
255 = 128+64+32+16+8+4+2+1 = 11111111
```

---

## Napomene za ispit 
- Network adresa i broadcast adresa **nisu korisne** za hostove
- Korisnih hosta = **2^n - 2** (n = broj host bitova)
- `/30` = točno 2 korisna hosta — standardno za **Point-to-Point linkove**
- **Magic Number** = 256 - vrijednost subnet maske u zadnjem oktetу
- **VLSM**: počni od **najveće** podmreže, idi prema manjima
- Privatne adrese: **10.x.x.x**, **172.16-31.x.x**, **192.168.x.x**
- Loopback: **127.0.0.1** — nikad ne izlazi iz uređaja
