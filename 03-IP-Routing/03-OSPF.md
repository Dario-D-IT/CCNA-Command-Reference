# 03 - OSPFv2 osnove

## Svrha
OSPF (Open Shortest Path First) je **Link-State** routing protokol koji koristi **Dijkstra SPF algoritam** za izračun najbolje putanje. Otvoreni standard — radi na uređajima svih proizvođača.

---

## OSI sloj
Radi na **Network Layeru (Layer 3)** i koristi IP protokol broj **89**.

---

## Tri glavna koraka rada

1. **Uspostava susjedstva:** Routeri šalju *Hello* pakete kako bi se međusobno pronašli
2. **Razmjena LSA:** Susjedi razmjenjuju **LSA (Link State Advertisements)** kako bi sinkronizirali baze podataka (**LSDB**)
3. **Izračun najbolje rute:** Svaki router pokreće SPF algoritam nad LSDB bazom kako bi izračunao najkraći put

---

## 1. Konfiguracija

### Mrežne naredbe

```bash
# Ulazak u OSPF proces
Router(config)# router ospf 1

# Točno određena IP adresa
Router(config-router)# network 10.1.1.1 0.0.0.0 area 0

# Cijeli subnet
Router(config-router)# network 10.1.1.0 0.0.0.255 area 0

# Sva sučelja na routeru
Router(config-router)# network 0.0.0.0 255.255.255.255 area 0

# Konfiguracija direktno na sučelju (alternativa)
Router(config-if)# ip ospf 1 area 0
```

### Dodatne postavke

**Pasivno sučelje**
Zaustavlja slanje Hello poruka na sučelje (npr. prema korisnicima), ali i dalje oglašava mrežu.
```bash
Router(config-router)# passive-interface GigabitEthernet0/0
# Postavi sva sučelja kao pasivna, pa uključi samo potrebna:
Router(config-router)# passive-interface default
Router(config-router)# no passive-interface GigabitEthernet0/1
```

**Loopback sučelje**
Virtualno sučelje koje služi za stabilan Router ID.
```bash
Router(config)# interface loopback 0
Router(config-if)# ip address 1.1.1.1 255.255.255.255
```

**Oglašavanje defaultne rute svim OSPF susjedima**
```bash
Router(config-router)# default-information originate
```

**Prilagodba reference bandwidth-a (za Gigabit mreže)**
```bash
Router(config-router)# auto-cost reference-bandwidth 1000
```

---

## 2. Tipovi OSPF mreža

| Tip | Opis | DR/BDR |
| :--- | :--- | :--- |
| **Broadcast** | Default na Ethernetu | Da |
| **Point-to-Point** | Između dva routera | Ne |
| **Non-Broadcast (NBMA)** | Npr. Frame Relay | Da |

---

## 3. Izbor DR/BDR i prioritet

- **DR** (Designated Router): Glavni router u segmentu — prikuplja i šalje LSA update-ove
- **BDR** (Backup Designated Router): Rezervni router
- **Redosljed izbora:** Najveći prioritet → najveći Router ID

```bash
# Postavljanje prioriteta (0 = nikad ne može postati DR/BDR)
Router(config-if)# ip ospf priority 255
```

---

## 4. Uvjeti za uspostavu susjedstva

Ovi parametri **moraju se podudarati**, inače se susjedstvo neće uspostaviti:
- Area ID
- Hello & Dead timeri
- Autentifikacijska lozinka
- Maska podmreže (samo na Broadcast mrežama)

**Uvjeti koji ne blokiraju susjedstvo, ali sprječavaju učenje ruta:**
- **MTU nepodudaranje** — susjedstvo zapne u ExStart/Exchange fazi
- **Nepodudaranje tipa mreže** — susjedstvo dođe do FULL stanja, ali SPF ne može izračunati rute

---

## 5. Izračun OSPF cost-a

OSPF koristi **Cost** kao metriku — računa se uvijek na **izlaznom (outgoing) sučelju**.

**Formula:**
```
Cost = Reference Bandwidth (default 100 Mbps) / Bandwidth sučelja
```

| Sučelje | Bandwidth | Cost (ref. 100 Mbps) |
| :--- | :--- | :--- |
| FastEthernet | 100 Mbps | 1 |
| Gigabit | 1000 Mbps | 1 (isti kao FA — zato treba povećati ref. BW!) |
| Serial | 1.544 Mbps | 64 |

---

## 6. Faze uspostave susjedstva (States)

| Stanje | Opis |
| :--- | :--- |
| **Down** | Početno stanje, Hello paketi još nisu razmijenjeni |
| **Init** | Hello paket je primljen, ali moj Router ID još nije u njemu |
| **Two-Way** | Dvosmjerna komunikacija — ovdje se odvija izbor DR/BDR |
| **ExStart** | Dogovor tko je Master, a tko Slave za razmjenu podataka |
| **Exchange** | Razmjena DBD paketa (sažetak sadržaja baze podataka) |
| **Loading** | Slanje LSR (zahtjev za detaljima) i primanje LSU (puni detalji) |
| **Full** | Sinkronizacija završena — baze su identične ✓ |

---

## 7. Tipovi OSPF poruka

| Tip | Naziv | Svrha |
| :---: | :--- | :--- |
| 1 | **Hello** | Pronalaženje susjeda i održavanje veze (keepalive) |
| 2 | **DBD** | Database Description — sažetak onoga što router zna |
| 3 | **LSR** | Link State Request — zahtjev za detaljima o ruti |
| 4 | **LSU** | Link State Update — sadrži pune LSA informacije |
| 5 | **LSAck** | Potvrda primitka LSU paketa |

---

## 8. Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ip ospf neighbor` | Popis susjeda i njihova stanja |
| `show ip ospf neighbor detail` | Detalji o svakom susjedu |
| `show ip route ospf` | OSPF rute u routing tablici (označene **O**) |
| `show ip ospf` | Informacije o OSPF procesu (Router ID, broj area-a) |
| `show ip ospf interface` | OSPF postavke po sučeljima (cost, timeri, DR/BDR) |
| `show ip ospf database` | Prikaz LSDB baze podataka |

---

## Napomene za ispit
- OSPF koristi **IP protokol 89** (nije TCP/UDP)
- Hello timer: **10s** (broadcast), Dead timer: **40s**; na serial linkovima: **30s/120s**
- **Router ID** = najveća IP loopback adresa → najveća IP fizičkog sučelja
- **Cost = 100 / bandwidth** — za Gigabit mreže povećaj reference bandwidth na 1000 ili 10000
- DR/BDR se biraju samo na **Broadcast** i **NBMA** mrežama
- Susjedstvo mora doseći **FULL** stanje da bi rute bile aktivne
- OSPF multicast adrese: **224.0.0.5** (svi OSPF routeri), **224.0.0.6** (DR/BDR)
