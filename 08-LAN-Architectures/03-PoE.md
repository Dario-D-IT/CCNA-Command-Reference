# 03 - PoE — Power over Ethernet

## Svrha
PoE omogućuje prijenos **električne energije zajedno s podatkovnim signalom** kroz isti Ethernet kabel. Eliminirajući potrebu za zasebnim napajanjem, pojednostavljuje instalaciju IP telefona, WiFi access pointova, IP kamera i sličnih uređaja.


---

## OSI sloj
PoE radi na **Physical Layeru (Layer 1)** — električna energija se prenosi kroz fizičke žice kabela.

---

## 1. IEEE PoE standardi

| Standard | Naziv | Maks. snaga (po portu) | Tipična primjena |
| :--- | :--- | :--- | :--- |
| **802.3af** | PoE | 15.4 W | IP telefoni, stariji AP-ovi |
| **802.3at** | PoE+ | 30 W | Noviji AP-ovi, PTZ kamere |
| **802.3bt** | PoE++ (4PPoE) | 60 W / 100 W | Tanki klijenti, videostijene, LED rasvjeta |

> **Napomena:** Snaga navedena je na **portu switcha (PSE)**. Uređaj koji se napaja (PD) prima nešto manje zbog gubitaka na kabelu.

---

## 2. Komponente PoE sustava

| Naziv | Kratica | Uloga |
| :--- | :--- | :--- |
| **Power Sourcing Equipment** | PSE | Uređaj koji isporučuje energiju (PoE switch, PoE injector) |
| **Powered Device** | PD | Uređaj koji prima energiju (IP telefon, AP, kamera) |

### PoE Injector
Ako switch ne podržava PoE, može se koristiti **PoE injector** između switcha i PD uređaja:
```
[Switch (bez PoE)] → [PoE Injector] → [IP telefon / AP]
```

---

## 3. PoE klase napajanja

Switch i uređaj pregovaraju klasu napajanja putem **CDP/LLDP** ili IEEE 802.3af/at/bt mehanizma:

| Klasa | Maks. snaga (PD) | Standard |
| :--- | :--- | :--- |
| Klasa 0 | 15.4 W | 802.3af (default) |
| Klasa 1 | 4 W | 802.3af |
| Klasa 2 | 7 W | 802.3af |
| Klasa 3 | 15.4 W | 802.3af |
| Klasa 4 | 30 W | 802.3at (PoE+) |
| Klasa 5-8 | 45–90 W | 802.3bt (PoE++) |

---

## 4. Konfiguracija

```bash
# Provjera podržanog PoE budžeta na switchu
Switch# show power inline

# Omogući PoE na specifičnom portu (obično je automatski uključen)
Switch(config)# interface FastEthernet0/1
Switch(config-if)# power inline auto             # Auto detekcija PD uređaja (default)

# Postavi maksimalnu snagu na portu (u mW)
Switch(config-if)# power inline auto max 15400   # Ograniči na 15.4W (802.3af)

# Isključi PoE na portu (za portove gdje nema PD uređaja)
Switch(config-if)# power inline never

# Statičko napajanje (bez pregovaranja — za uređaje koji ne podržavaju CDP/LLDP)
Switch(config-if)# power inline static
```

---

## 5. PoE budžet switcha

Svaki PoE switch ima **ukupni PoE budžet** (npr. 370 W). Zbroj snage svih spojenih PD uređaja ne smije premašiti taj budžet.

**Primjer planiranja:**
```
Switch: 24-port PoE+, budžet = 370 W
24 x IP telefon (klasa 2, 7 W) = 168 W  ✓
8 x WiFi AP (klasa 4, 15.4 W)  = 123 W  ✓
Ukupno: 291 W < 370 W           ✓
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show power inline` | Ukupni PoE budžet i status svih PoE portova |
| `show power inline FastEthernet0/1` | Detalji PoE za specifični port (napajanje, klasa, uređaj) |
| `show power inline detail` | Detaljni prikaz svakog porta |

**Primjer outputa:**
```
Switch# show power inline
Available:370.0(w)  Used:45.0(w)  Remaining:325.0(w)

Interface  Admin  Oper   Power     Device              Class
---------  -----  ----   -----     ------              -----
Fa0/1      auto   on     7.000     IP Phone 7960       2
Fa0/2      auto   on     15.400    AIR-AP2802I         4
Fa0/3      auto   off    0.000     n/a                 n/a
```

---

## Napomene za ispit
- **802.3af** = 15.4 W, **802.3at (PoE+)** = 30 W, **802.3bt (PoE++)** = 60/100 W
- **PSE** = daje energiju (switch), **PD** = prima energiju (telefon, AP)
- `power inline auto` = automatska detekcija PD uređaja (default)
- `power inline never` = isključi PoE na portu
- Uvijek provjeri **ukupni PoE budžet** switcha pri planiranju instalacije
- PoE radi na standardnom **Cat5e/Cat6** kabelu — nije potreban poseban kabel
- Maksimalna duljina kabela za PoE: **100 metara** (kao i za standardni Ethernet)
