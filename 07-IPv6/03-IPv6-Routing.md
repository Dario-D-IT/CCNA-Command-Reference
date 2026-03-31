# 03 - IPv6 Usmjeravanje

## Svrha
Konfiguracija usmjeravanja IPv6 prometa — statičke rute i dinamički protokol OSPFv3. Logika je identična IPv4, samo je sintaksa drugačija.

---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## 1. Statičke IPv6 rute

### Sintaksa
```bash
Router(config)# ipv6 route [odredišni-prefiks/duljina] [next-hop ILI exit-interface]
```

### Primjeri
```bash
# Standardna statička ruta (next-hop je Global Unicast adresa)
Router(config)# ipv6 route 2001:DB8:ACAD:2::/64 2001:DB8:ACAD:12::2

# Statička ruta putem exit sučelja (za point-to-point linkove)
Router(config)# ipv6 route 2001:DB8:ACAD:2::/64 GigabitEthernet0/1

# Statička ruta s exit sučeljem i next-hopom (preporučeno za Ethernet)
Router(config)# ipv6 route 2001:DB8:ACAD:2::/64 GigabitEthernet0/0 2001:DB8:ACAD:12::2

# Defaultna IPv6 ruta (sve prema ISP-u)
Router(config)# ipv6 route ::/0 2001:DB8:ACAD:12::2

# Floating static route (AD = 5, backup ruta)
Router(config)# ipv6 route 2001:DB8:ACAD:2::/64 2001:DB8:ACAD:12::2 5
```

> **Napomena:** Ako koristiš Link-Local adresu kao next-hop, **moraš navesti i exit sučelje**:
> ```bash
> Router(config)# ipv6 route 2001:DB8:ACAD:2::/64 GigabitEthernet0/0 FE80::2
> ```

---

## 2. OSPFv3 — OSPF za IPv6

OSPFv3 je verzija OSPF protokola prilagođena za IPv6. Logika rada je ista kao OSPFv2, ali:
- Radi isključivo s **IPv6**
- Konfiguracija se vrši **direktno na sučeljima** (ne putem `network` naredbe)
- Koristi **Link-Local adrese** za komunikaciju između susjeda

### Konfiguracija OSPFv3
```bash
# Korak 1: Uključi IPv6 usmjeravanje
Router(config)# ipv6 unicast-routing

# Korak 2: Kreiraj OSPFv3 proces
Router(config)# ipv6 router ospf 1
Router(config-rtr)# router-id 1.1.1.1             # Router ID je još uvijek u IPv4 formatu!

# Korak 3: Omogući OSPF direktno na sučelju
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 ospf 1 area 0

# Pasivno sučelje (ne šalje Hello poruke prema korisnicima)
Router(config)# ipv6 router ospf 1
Router(config-rtr)# passive-interface GigabitEthernet0/1
```

---

## 3. IPv6 u routing tablici

```bash
Router# show ipv6 route
```

**Oznake u tablici:**

| Oznaka | Izvor rute |
| :--- | :--- |
| `C` | Directly Connected |
| `L` | Local (adresa sučelja samog routera) |
| `S` | Static |
| `O` | OSPFv3 |
| `D` | EIGRP |

**Primjer outputa:**
```
Router# show ipv6 route
IPv6 Routing Table - default - 5 entries
C   2001:DB8:ACAD:1::/64 [0/0]
     via GigabitEthernet0/0, directly connected
L   2001:DB8:ACAD:1::1/128 [0/0]
     via GigabitEthernet0/0, receive
S   2001:DB8:ACAD:2::/64 [1/0]
     via 2001:DB8:ACAD:12::2
S   ::/0 [1/0]
     via 2001:DB8:ACAD:12::2
```

> **Napomena:** IPv6 routing tablica uvijek ima **C i L** unos za svako aktivno sučelje:
> - `C` = mreža sučelja (/64)
> - `L` = točna adresa sučelja (/128)

---

## 4. NDP — Neighbor Discovery Protocol

NDP zamjenjuje ARP iz IPv4. Koristi ICMPv6 poruke:

| Poruka | Svrha | IPv4 analogija |
| :--- | :--- | :--- |
| **RS** (Router Solicitation) | Klijent traži router | — |
| **RA** (Router Advertisement) | Router oglašava prefiks i gateway | — |
| **NS** (Neighbor Solicitation) | Tko ima ovu IPv6 adresu? | ARP Request |
| **NA** (Neighbor Advertisement) | Ja imam tu adresu! | ARP Reply |
| **Redirect** | Bolja ruta postoji | ICMP Redirect |

```bash
# Provjera NDP tablice (analogna ARP tablici)
Router# show ipv6 neighbors
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ipv6 route` | Cijela IPv6 routing tablica |
| `show ipv6 route static` | Samo statičke IPv6 rute |
| `show ipv6 route ospf` | Samo OSPFv3 rute |
| `show ipv6 ospf neighbor` | OSPFv3 susjedi i njihova stanja |
| `show ipv6 ospf interface` | OSPFv3 detalji po sučeljima |
| `show ipv6 neighbors` | NDP tablica (MAC ↔ IPv6) |
| `ping ipv6 2001:DB8::1` | Testiranje IPv6 dostupnosti |

---

## Napomene za ispit
- Defaultna IPv6 ruta: `::/0` (analogno `0.0.0.0/0` u IPv4)
- Ako je next-hop **Link-Local** adresa — obavezno navedi i **exit sučelje**
- **OSPFv3 Router ID** je i dalje u **IPv4 formatu** (npr. `1.1.1.1`)
- OSPFv3 konfigurira se **na sučelju** (`ipv6 ospf 1 area 0`), ne putem `network` naredbe
- IPv6 routing tablica ima uvijek **C + L** unos za svako aktivno sučelje
- **NDP** (`show ipv6 neighbors`) = IPv6 ekvivalent ARP tablice
- OSPFv3 koristi multicast `FF02::5` (svi OSPF routeri) i `FF02::6` (DR/BDR)
