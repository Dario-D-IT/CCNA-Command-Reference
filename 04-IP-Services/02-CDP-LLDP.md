# CDP & LLDP — Protokoli za otkrivanje susjeda

## Svrha
Protokoli koji uređajima omogućuju automatsko **otkrivanje direktno spojenih susjeda** bez ikakve konfiguracije. Korisni za dokumentiranje mreže i troubleshooting.

- **CDP** (Cisco Discovery Protocol) — Cisco proprietarni protokol
- **LLDP** (Link Layer Discovery Protocol) — IEEE 802.1AB, industry standard, radi na svim proizvođačima


---

## OSI sloj
Radi na **Data Link Layeru (Layer 2)**.
Šalje poruke direktno na multicast MAC adresu — ne prolazi kroz routere.

- CDP multicast: `01:00:0C:CC:CC:CC`
- LLDP multicast: `01:80:C2:00:00:0E`

---

## Konfiguracija

### CDP

```bash
# CDP je uključen globalno po defaultu na Cisco uređajima
# Isključivanje globalno (sigurnosna praksa na rubnim portovima)
Router(config)# no cdp run

# Uključivanje globalno
Router(config)# cdp run

# Isključivanje na specifičnom sučelju (prema end-uređajima, nije susjedni switch)
Router(config-if)# no cdp enable

# Uključivanje na specifičnom sučelju
Router(config-if)# cdp enable
```

### LLDP

```bash
# LLDP je isključen po defaultu — treba ga ručno uključiti
Router(config)# lldp run

# Isključivanje LLDP transmisije na sučelju
Router(config-if)# no lldp transmit

# Isključivanje LLDP prijema na sučelju
Router(config-if)# no lldp receive
```

---

## Verifikacija

### CDP verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show cdp` | Globalni CDP status i timeri |
| `show cdp neighbors` | Kratak popis susjeda (hostname, port, capability) |
| `show cdp neighbors detail` | Detalji: IP adresa, IOS verzija, platforma |
| `show cdp interface` | CDP status po sučeljima |
| `show cdp entry *` | Isto kao neighbors detail |

### LLDP verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show lldp` | Globalni LLDP status i timeri |
| `show lldp neighbors` | Kratak popis LLDP susjeda |
| `show lldp neighbors detail` | Detalji susjeda |
| `show lldp interface` | LLDP status po sučeljima (TX/RX) |

---

## Napomene za ispit
- **CDP je Cisco-only**, LLDP radi s bilo kojim proizvođačem
- CDP je **uključen po defaultu**, LLDP je **isključen po defaultu**
- Oba rade na **Layer 2** — ne routaju se
- CDP šalje poruke svakih **60 sekundi**, holdtime je **180 sekundi**
- LLDP šalje poruke svakih **30 sekundi**, holdtime je **120 sekundi**
- Sigurnosna preporuka: isključiti CDP/LLDP prema end-uređajima (`no cdp enable` na access portovima)
