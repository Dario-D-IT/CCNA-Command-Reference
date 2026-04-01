# 02 - IPv6 Konfiguracija uređaja

## Svrha
Konfiguracija IPv6 adresa na Cisco uređajima i automatske metode dodjele adresa krajnjim uređajima (SLAAC i DHCPv6).

---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## 1. Uključivanje IPv6 usmjeravanja

```bash
# Obavezno na routerima — bez ove naredbe router ne routira IPv6 promet
Router(config)# ipv6 unicast-routing
```

---

## 2. Ručna konfiguracija IPv6 adrese

```bash
Router(config)# interface GigabitEthernet0/0

# Postavljanje IPv6 adrese s prefiksom
Router(config-if)# ipv6 address 2001:DB8:ACAD:1::1/64

# Ručno postavljanje Link-Local adrese (opcionalno — inače se generira automatski)
Router(config-if)# ipv6 address FE80::1 link-local

Router(config-if)# no shutdown
```

---

## 3. EUI-64 automatska konfiguracija

Router automatski generira Interface ID iz svoje MAC adrese:

```bash
Router(config-if)# ipv6 address 2001:DB8:ACAD:1::/64 eui-64
```

---

## 4. SLAAC — Stateless Address Autoconfiguration

SLAAC omogućuje krajnjim uređajima da **sami konfiguriraju IPv6 adresu** bez DHCP servera.

### Kako radi SLAAC

```
1. Klijent šalje RS (Router Solicitation) → FF02::2 (svi routeri)
2. Router odgovara RA (Router Advertisement) s prefiksom mreže
3. Klijent uzima taj prefiks + sam generira Interface ID (EUI-64 ili random)
4. Klijent dobiva: IPv6 adresu + prefix length + default gateway (Link-Local routera)
```

### Konfiguracija na routeru (RA oglašavanje)
```bash
# Router automatski šalje RA poruke ako je ipv6 unicast-routing uključen
# Ručno pokretanje RA oglašavanja na sučelju:
Router(config-if)# ipv6 nd ra interval 200

# Zaustavljanje RA oglašavanja (za portove prema korisnicima gdje nema routera)
Router(config-if)# ipv6 nd ra suppress
```

---

## 5. DHCPv6

DHCPv6 postoji u dva načina rada:

| Način | Naziv | Što server dodjeljuje |
| :--- | :--- | :--- |
| **Stateless** | DHCPv6 Stateless | Samo DNS i druge opcije (ne adresu — tu radi SLAAC) |
| **Stateful** | DHCPv6 Stateful | IPv6 adresu + DNS + sve opcije (kao DHCPv4) |

### RA zastavice koje kontroliraju način rada

| Zastavica | Naziv | Značenje |
| :---: | :--- | :--- |
| **M** | Managed | `1` = koristi Stateful DHCPv6 za adresu |
| **O** | Other | `1` = koristi DHCPv6 za ostale opcije (DNS) |

```
M=0, O=0  →  Čisti SLAAC (bez DHCPv6)
M=0, O=1  →  SLAAC + Stateless DHCPv6 (adresa od SLAAC, DNS od DHCP)
M=1, O=1  →  Stateful DHCPv6 (sve od DHCP servera)
```

### Konfiguracija DHCPv6 Stateless servera
```bash
# Kreiraj DHCPv6 pool (samo DNS, bez adresa)
Router(config)# ipv6 dhcp pool STATELESS-POOL
Router(config-dhcpv6)# dns-server 2001:DB8::53
Router(config-dhcpv6)# domain-name tvrtka.hr

# Primijeni na sučelje i postavi O zastavicu
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 dhcp server STATELESS-POOL
Router(config-if)# ipv6 nd other-config-flag             # Postavi O=1
```

### Konfiguracija DHCPv6 Stateful servera
```bash
# Kreiraj DHCPv6 pool s rasponom adresa
Router(config)# ipv6 dhcp pool STATEFUL-POOL
Router(config-dhcpv6)# address prefix 2001:DB8:ACAD:1::/64 lifetime 172800 86400
Router(config-dhcpv6)# dns-server 2001:DB8::53
Router(config-dhcpv6)# domain-name tvrtka.hr

# Primijeni na sučelje i postavi M zastavicu
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 dhcp server STATEFUL-POOL
Router(config-if)# ipv6 nd managed-config-flag           # Postavi M=1
```

### DHCPv6 Relay (kada server nije na istom subnetu)
```bash
Router(config-if)# ipv6 dhcp relay destination 2001:DB8::DHCP-SERVER
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ipv6 interface brief` | Popis IPv6 adresa po sučeljima |
| `show ipv6 interface GigabitEthernet0/0` | Detalji IPv6 konfiguracije sučelja |
| `show ipv6 dhcp pool` | DHCPv6 pool i dodijeljene adrese |
| `show ipv6 dhcp binding` | Popis klijenata i dodijeljenih adresa (Stateful) |
| `show ipv6 neighbors` | NDP tablica (analogna ARP tablici u IPv4) |

---

## Napomene za ispit
- `ipv6 unicast-routing` je **obavezan** na routeru za IPv6 usmjeravanje
- **SLAAC** = klijent sam gradi adresu iz RA prefiksa + EUI-64/random
- **M=1** = koristi Stateful DHCPv6, **O=1** = koristi DHCPv6 samo za opcije
- **NDP** (Neighbor Discovery Protocol) zamjenjuje ARP iz IPv4
- IPv6 nema **broadcast** — SLAAC koristi multicast `FF02::2` (svi routeri)
- DHCPv6 koristi UDP portove **546** (klijent) i **547** (server)
