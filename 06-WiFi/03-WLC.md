# 03 - WLC — Wireless LAN Controller

## Svrha
WLC (Wireless LAN Controller) centralizira upravljanje svim **Lightweight AP-ovima (LAP)** u mreži. Umjesto da konfiguriraš svaki AP zasebno, sve se konfigurira na jednom mjestu — WLC-u.


---

## OSI sloj
CAPWAP tuneli rade na **Layer 3 (Network)**.
Bežična komunikacija prema klijentima radi na **Layer 2**.

---

## 1. Autonomous vs Lightweight arhitektura

### Autonomous AP
```
[Klijent] ←→ [AP] ←→ [Switch] ←→ [Mreža]
         Sva logika je na AP-u
```

### Lightweight AP + WLC (Split-MAC)
```
[Klijent] ←→ [LAP] ←=CAPWAP tunel=→ [WLC] ←→ [Switch] ←→ [Mreža]
              RF signal              Sva logika je na WLC-u
```

| Funkcija | Gdje se obrađuje |
| :--- | :--- |
| Slanje/primanje RF signala | **AP** |
| Enkripcija/dekripcija podataka | **AP** |
| Autentifikacija klijenata | **WLC** |
| Upravljanje kanalima i snagom | **WLC** |
| Roaming između AP-ova | **WLC** |
| Sigurnosne politike | **WLC** |

---

## 2. CAPWAP protokol

**CAPWAP** (Control And Provisioning of Wireless Access Points) je tunel između LAP-a i WLC-a.

```
CAPWAP Control tunel  → UDP 5246  (upravljačke poruke, enkripcija DTLS)
CAPWAP Data tunel     → UDP 5247  (podatkovni promet klijenata)
```

### Kako LAP pronalazi WLC (Discovery proces)
1. **DHCP Option 43** — DHCP server vraća IP adresu WLC-a
2. **DNS** — razrješavanje naziva `CISCO-CAPWAP-CONTROLLER.lokalna.domena`
3. **Subnet broadcast** — LAP šalje broadcast na lokalnom subnetu
4. **Pohranjena lista** — LAP pamti WLC iz prethodnog spajanja

---

## 3. Načini rada LAP-a (AP Modes)

| Mod | Opis |
| :--- | :--- |
| **Local** | Normalan rad — servira klijente, skenira pozadinski |
| **FlexConnect** | AP može lokalno prosljeđivati promet i bez WLC-a |
| **Monitor** | Samo pasivno skenira RF okruženje (rogue AP detekcija) |
| **Sniffer** | Snima pakete i šalje ih na analizator |
| **Rogue Detector** | Detektira neovlaštene AP-ove u žičnoj mreži |
| **Bridge** | Point-to-point ili point-to-multipoint bežični most |

---

## 4. VLAN/SSID mapiranje

Svaki SSID se mapira na zasebni VLAN — korisnici različitih SSID-ova su logički odvojeni.

```
SSID "Tvrtka-Zaposleni"  →  VLAN 10  (interni pristup)
SSID "Tvrtka-Gosti"      →  VLAN 20  (samo Internet)
SSID "Tvrtka-IoT"        →  VLAN 30  (izolirani uređaji)
```

---

## 5. Osnovna konfiguracija WLC-a (GUI workflow)

WLC se uglavnom konfigurira putem **web sučelja (HTTPS GUI)**.

```
1. Spoji se na WLC: https://[IP-WLC]
2. Controller → Interfaces → Kreiraj management i AP Manager sučelje
3. WLANs → Create New → Definiraj SSID, VLAN, sigurnosni profil
4. Wireless → Access Points → Provjeri jesu li AP-ovi spojeni
5. Security → AAA → Postavi RADIUS server (za Enterprise)
```

### Osnovna CLI konfiguracija (za inicijalno postavljanje)
```bash
# Inicijalni setup wizard (pri prvom pokretanju)
Would you like to terminate autoinstall? [yes]: yes
System Name: WLC-HQ
Enter Administrative User Name: admin
Enter Administrative Password: Lozinka123!
Management Interface IP Address: 192.168.1.10
Management Interface Netmask: 255.255.255.0
Management Interface Default Router: 192.168.1.1
```

---

## Verifikacija (CLI)

| Naredba | Svrha |
| :--- | :--- |
| `show ap summary` | Popis svih spojenih AP-ova i njihov status |
| `show wlan summary` | Popis konfiguriranih WLAN-ova (SSID-ova) |
| `show client summary` | Popis spojenih bežičnih klijenata |
| `show interface summary` | Popis WLC sučelja (management, AP-manager, VLAN) |
| `show ap join stats summary` | Statistike spajanja AP-ova na WLC |
| `debug capwap events enable` | Debug CAPWAP tunel uspostave |

---

## Napomene za ispit
- **CAPWAP Control**: UDP **5246**, **CAPWAP Data**: UDP **5247**
- LAP traži WLC redom: **DHCP Option 43 → DNS → Broadcast → Pohranjena lista**
- **FlexConnect** = LAP može raditi i bez WLC-a (lokalno prosljeđivanje)
- **Local mod** = normalni rad AP-a u centralnom načinu
- Svaki SSID mapira se na **zasebni VLAN** za segmentaciju prometa
- WLC se primarno konfigurira putem **HTTPS GUI**
- **Roaming** između AP-ova je transparentan za klijenta — WLC to koordinira
