# 02 - Dinamičko usmjeravanje (Dynamic Routing)

## Svrha
Dinamičko usmjeravanje omogućuje routerima da **automatski razmjenjuju informacije** o mrežama i sami pronalaze najbolje putanje. Ako jedan link padne, protokoli će automatski izračunati novi put.


---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## 1. Klasifikacija protokola (IGP vs EGP)

Dinamički routing protokoli dijele se prema tome upravljaju li mrežom unutar jedne organizacije ili između njih:

| Tip | Opis | Primjeri |
| :--- | :--- | :--- |
| **IGP** (Interior Gateway Protocol) | Koristi se unutar jednog autonomnog sustava | OSPF, EIGRP, RIP |
| **EGP** (Exterior Gateway Protocol) | Koristi se za spajanje različitih autonomnih sustava (npr. na Internet) | BGP |

---

## 2. Algoritmi rada

| Algoritam | Protokol | Opis |
| :--- | :--- | :--- |
| **Distance Vector** | RIP | Router vidi samo smjer i udaljenost (broj skokova). Ne poznaje cijelu mapu mreže. |
| **Link State** | OSPF | Svaki router ima kompletnu mapu cijele mreže (LSDB). Brže reagira na promjene. |
| **Advanced Distance Vector** | EIGRP | Cisco hibridni protokol — kombinira Distance Vector i Link State pristup. |

---

## 3. Metrika (mjera puta)

Svaki protokol koristi svoju "metriku" kako bi odlučio koji je put najbolji:

| Protokol | Metrika |
| :--- | :--- |
| **RIP** | Hop Count (broj routera do cilja, max 15) |
| **OSPF** | Cost (baziran na propusnosti linka) |
| **EIGRP** | Bandwidth + Delay (kašnjenje) |

---

## 4. Administrative Distance (AD) tablica

Kada router nauči put do iste mreže putem više različitih protokola, koristi **AD** za odluku kome više vjeruje. **Manji broj = veće povjerenje.**

| Izvor rute | Administrative Distance (AD) |
| :--- | :--- |
| **Direktno spojen (Connected)** | 0 |
| **Statička ruta** | 1 |
| **EIGRP Summary** | 5 |
| **External BGP** | 20 |
| **EIGRP (interni)** | 90 |
| **OSPF** | 110 |
| **RIP** | 120 |

---

## 5. Osnovna konfiguracija (primjer OSPF)

```bash
# Ulazak u proces routing protokola
Router(config)# router ospf 1

# Oglašavanje mreža koje pripadaju routeru
Router(config-router)# network 192.168.10.0 0.0.0.255 area 0
```

---

## 6. Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ip protocols` | Prikazuje koji su protokoli aktivni i njihove parametre |
| `show ip route` | Prikazuje cijelu routing tablicu (dinamičke rute: **O**, **D**, **R**) |
| `show ip route ospf` | Filtrira tablicu — prikazuje samo OSPF rute |

---

## Napomene za ispit
- **Manji AD = veće povjerenje** — Connected (0) uvijek pobjeđuje Static (1)
- RIP maksimalni hop count = **15** (16 = nedostižno)
- OSPF koristi **Cost = 100 Mbps / bandwidth** (reference bandwidth)
- **IGP** = unutar organizacije, **EGP (BGP)** = između organizacija/provajdera
- Dinamički protokoli automatski reagiraju na **promjene topologije** — statički ne
