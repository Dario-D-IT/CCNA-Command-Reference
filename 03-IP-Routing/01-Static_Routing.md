# 01 - Statičko usmjeravanje (Static Routing)

## Svrha
Statičko usmjeravanje je **ručno konfiguriranje putanja** u routing tablici. Za razliku od dinamičkih protokola, router ovdje ne "razgovara" s drugima, već slijedi fiksne upute koje si ti postavio.

---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## 1. Tipovi statičkih ruta

| Tip | Opis |
| :--- | :--- |
| **Standardna statička ruta** | Ruta do točno određene udaljene mreže |
| **Default statička ruta (0.0.0.0/0)** | "Gateway of last resort" — sav nepoznati promet šalje ovuda |
| **Floating statička ruta** | Rezervna ruta s višim AD — aktivira se samo ako primarna padne |
| **Summary statička ruta** | Jedna ruta koja obuhvaća više manjih mreža |

---

## 2. Konfiguracija

```bash
# Sintaksa: ip route [mreža] [maska] [next-hop IP ILI exit interface]

# 1. Standardna statička ruta
Router(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2

# 2. Defaultna ruta (prema Internetu)
Router(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2

# 3. Floating static route (AD = 210, aktivira se ako primarna padne)
Router(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2 210
```

---

## 3. Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ip route` | Prikazuje cijelu routing tablicu |
| `show ip route static` | Prikazuje samo ručno unesene rute (označene slovom **S**) |
| `show ip route 0.0.0.0` | Provjera defaultne rute (označena s **S\***) |

---

## Napomene za ispit
- Statička ruta ima **AD = 1** (samo Connected interface ima manji, AD = 0)
- **Floating static route** mora imati **viši AD** od primarne rute da bi ostala neaktivna
- `0.0.0.0 0.0.0.0` = odgovara svim odredištima (default ruta)
- Statičke rute su označene slovom **S** u routing tablici, default je **S\***
- Prednost: puna kontrola; Nedostatak: ne prilagođava se automatski promjenama topologije

