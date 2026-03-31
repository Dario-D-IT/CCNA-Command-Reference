# 01 - IPv6 Adresiranje

## Svrha
IPv6 je nasljednik IPv4 protokola, razvijen zbog **nedostatka slobodnih IPv4 adresa**. Pruža gotovo neograničen broj adresa i donosi pojednostavljenu konfiguraciju.

---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## 1. Format IPv6 adrese

IPv6 adresa je dugačka **128 bita**, zapisana kao **8 grupa po 16 bita**, odvojenih dvotočkama (`:`).

```
Primjer:  2001:0DB8:0000:0001:0000:0000:0000:0001
Grupe:    [  1 ][  2 ][  3 ][  4 ][  5 ][  6 ][  7 ][  8 ]
```

---

## 2. Pravila skraćivanja

### Pravilo 1 — Izostavi vodeće nule unutar grupe
```
2001:0DB8:0000:0001:0000:0000:0000:0001
→    2001:DB8:0:1:0:0:0:1
```

### Pravilo 2 — Zamijeni jednu ili više uzastopnih grupa nula s `::`
```
2001:DB8:0:1:0:0:0:1
→    2001:DB8:0:1::1
```

> ⚠️ `::` se smije koristiti **samo jednom** u adresi — inače nije jasno koliko nula zamjenjuje.

### Primjeri skraćivanja
```
Puna adresa:        2001:0DB8:0000:0000:0000:0000:0000:0001
Skraćeno:           2001:DB8::1

Puna adresa:        FE80:0000:0000:0000:0A00:27FF:FE8D:1234
Skraćeno:           FE80::A00:27FF:FE8D:1234
```

---

## 3. Prefix i duljina prefiksa

IPv6 ne koristi subnet maske — koristi **prefix length** (kao CIDR kod IPv4):

```
2001:DB8:ACAD:1::1/64

Prefix (mrežni dio):    2001:DB8:ACAD:1::   (/64 = prvih 64 bita)
Interface ID (host):    ::1                  (zadnjih 64 bita)
```

Standardna duljina prefiksa za LAN subnet: **/64**

---

## 4. Tipovi IPv6 adresa

| Tip | Prefix | Opis | IPv4 analogija |
| :--- | :--- | :--- | :--- |
| **Global Unicast (GUA)** | `2000::/3` | Javna, globalno usmjeriva adresa | Javna IPv4 adresa |
| **Link-Local** | `FE80::/10` | Samo unutar jednog linka, ne usmjeriva | 169.254.x.x (APIPA) |
| **Unique Local** | `FC00::/7` | Privatna adresa, nije globalno usmjeriva | RFC 1918 (192.168.x.x) |
| **Loopback** | `::1/128` | Lokalna adresa uređaja | 127.0.0.1 |
| **Unspecified** | `::/128` | Nespecificirana adresa | 0.0.0.0 |
| **Multicast** | `FF00::/8` | Slanje grupi primatelja | 224.0.0.0/4 |

### Važne Multicast adrese

| Adresa | Primatelji |
| :--- | :--- |
| `FF02::1` | Svi IPv6 uređaji na linku |
| `FF02::2` | Svi IPv6 routeri na linku |
| `FF02::5` | Svi OSPF routeri |
| `FF02::6` | OSPF DR/BDR routeri |

---

## 5. EUI-64 — automatsko generiranje Interface ID-a

EUI-64 generira 64-bitni Interface ID iz **48-bitne MAC adrese**:

```
Korak 1: Uzmi MAC adresu
          AA:BB:CC:DD:EE:FF

Korak 2: Umetni "FFFE" u sredinu
          AA:BB:CC:FF:FE:DD:EE:FF

Korak 3: Invertiraj 7. bit (Universal/Local bit) prvog bajta
          AA = 10101010  →  invertiraj bit 7  →  10101000 = A8

Rezultat Interface ID:  A8BB:CCFF:FEDD:EEFF
Cijela adresa:          2001:DB8:ACAD:1:A8BB:CCFF:FEDD:EEFF/64
```

---

## 6. Link-Local adrese

- Automatski se generiraju na **svakom IPv6 sučelju**
- Prefiks uvijek: `FE80::/10`
- Ne usmjeravaju se izvan lokalnog linka
- Koriste se za: **default gateway**, **NDP**, **routing protokoli**

```bash
# Provjera Link-Local adrese
Router# show ipv6 interface GigabitEthernet0/0
```

---

## Napomene za ispit
- IPv6 adresa = **128 bita** (vs IPv4 = 32 bita)
- `::` zamjenjuje uzastopne nulte grupe — **samo jednom** po adresi
- **Link-Local** (`FE80::/10`) je uvijek prisutna, ne usmjerava se
- **GUA** (`2000::/3`) = javna adresa — globalno usmjeriva
- Standard subnet duljina = **/64** (64 bita mreža + 64 bita host)
- EUI-64: MAC (48-bit) + `FFFE` u sredini + invertirani 7. bit = Interface ID (64-bit)
- IPv6 **nema broadcast** — zamijenjeno multicastom (`FF02::1` = svi uređaji)
