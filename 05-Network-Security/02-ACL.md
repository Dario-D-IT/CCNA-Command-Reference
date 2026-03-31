# 02 - ACL — Access Control Lists

## Svrha
ACL-ovi su **filteri prometa** koji dozvoljavaju ili odbijaju pakete na temelju definiranih pravila. Koriste se za:
- Sigurnost (blokiranje neovlaštenog prometa)
- Ograničavanje pristupa mrežnim servisima
- Klasifikaciju prometa za QoS i NAT


---

## OSI sloj
Radi na **Network Layeru (Layer 3)** — Standard ACL.
Radi na **Layer 3 + Layer 4** — Extended ACL (gleda i TCP/UDP portove).

---

## Tipovi ACL-ova

| Tip | Broj | Filtrira prema |
| :--- | :--- | :--- |
| **Standard** | 1–99, 1300–1999 | Samo izvorišna IP adresa |
| **Extended** | 100–199, 2000–2699 | Izvorišna IP, odredišna IP, protokol, port |
| **Named** | Bilo koji naziv | Isti kao Standard ili Extended, ali s imenom |

---

## Logika rada ACL-a

```
Paket dolazi → Provjeri pravilo 1 → Poklapa se? → Dozvoli/Odbij → KRAJ
                                  ↓ Ne
                    Provjeri pravilo 2 → ...
                                  ↓ Nijedan nije odgovarao
                         IMPLICIT DENY (odbij sve)
```

> ⚠️ **Implicitni deny** — na kraju svakog ACL-a postoji nevidljivo pravilo koje odbija sve. Uvijek dodaj `permit any` ako to nije namjera!

---

## Wildcard maske

Wildcard maska je **obrnuta** od subnet maske:
```
Subnet maska:   255.255.255.0   →  Wildcard: 0.0.0.255
Subnet maska:   255.255.0.0     →  Wildcard: 0.0.255.255
```

| Wildcard | Značenje |
| :--- | :--- |
| `0.0.0.0` | Točno ta IP adresa (isti kao `host`) |
| `255.255.255.255` | Bilo koja IP adresa (isti kao `any`) |
| `0.0.0.255` | Cijeli /24 subnet |

---

## Konfiguracija

### Standard ACL (numbered)
```bash
# Dozvoli samo mrežu 192.168.1.0/24, sve ostalo odbij
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# access-list 10 deny any              # Nije potrebno (implicitno), ali dobra praksa

# Primjena na sučelje (Standard ACL — postavi što bliže ODREDIŠTU)
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip access-group 10 out            # OUT = izlazni promet
```

### Extended ACL (numbered)
```bash
# Dozvoli HTTP (80) i HTTPS (443) promet s mreže 192.168.1.0/24 prema bilo gdje
Router(config)# access-list 110 permit tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config)# access-list 110 permit tcp 192.168.1.0 0.0.0.255 any eq 443
Router(config)# access-list 110 deny ip any any       # Eksplicitni deny za logove

# Primjena na sučelje (Extended ACL — postavi što bliže IZVORU)
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip access-group 110 in             # IN = ulazni promet
```

### Named ACL (preporučeno — lakše uređivanje)
```bash
# Kreiranje named standard ACL
Router(config)# ip access-list standard DOZVOLI_UPRAVLJANJE
Router(config-std-nacl)# permit 10.0.0.0 0.0.0.255
Router(config-std-nacl)# deny any

# Kreiranje named extended ACL
Router(config)# ip access-list extended BLOKIRAJ_TELNET
Router(config-ext-nacl)# deny tcp any any eq 23       # Blokiraj Telnet
Router(config-ext-nacl)# permit ip any any            # Sve ostalo dozvoli

# Primjena
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip access-group BLOKIRAJ_TELNET in
```

### ACL za zaštitu VTY linija (SSH/Telnet pristup)
```bash
Router(config)# ip access-list standard DOZVOLI_SSH
Router(config-std-nacl)# permit 10.0.0.0 0.0.0.255   # Samo ova mreža može pristupiti

Router(config)# line vty 0 15
Router(config-line)# access-class DOZVOLI_SSH in      # access-class za VTY, ne access-group!
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show access-lists` | Prikaz svih ACL-ova i broja pogodaka (matches) |
| `show access-lists 110` | Prikaz specifičnog ACL-a |
| `show ip interface GigabitEthernet0/0` | Koji ACL je primjenjen na sučelju i u kojem smjeru |
| `show running-config | include access` | Filtriranje konfiguracije za ACL |

---

## Napomene za ispit
- **Standard ACL** — postavi **blizu odredišta** (filtrira samo po izvoru, moglo bi blokirati previše)
- **Extended ACL** — postavi **blizu izvora** (specifičniji, sprječava nepotrebno putovanje paketa)
- Na svakom sučelju može biti **jedan ACL po smjeru** (jedan IN, jedan OUT)
- `host 192.168.1.1` = `192.168.1.1 0.0.0.0`
- `any` = `0.0.0.0 255.255.255.255`
- Redosljed pravila je **kritičan** — specifičnija pravila stavi **na vrh**
- Named ACL-ovi omogućuju **brisanje pojedinih linija** (`no 10` briše liniju 10)
- `access-class` se koristi za VTY linije, `access-group` za fizička sučelja
