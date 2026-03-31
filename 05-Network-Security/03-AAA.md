# 03 - AAA — Autentifikacija, Autorizacija i Accounting

## Svrha
AAA je okvir za centralizirano upravljanje pristupom mrežnim uređajima:
- **Autentifikacija (Authentication)** — *Tko si ti?* (provjera identiteta)
- **Autorizacija (Authorization)** — *Što smiješ raditi?* (prava i dopuštenja)
- **Obračun (Accounting)** — *Što si radio?* (bilježenje aktivnosti)


---

## OSI sloj
Radi na **Application Layeru (Layer 7)**.

---

## AAA protokoli

| Protokol | Transport | Port | Enkripcija | Razvio |
| :--- | :--- | :--- | :--- | :--- |
| **RADIUS** | UDP | 1812 (auth), 1813 (accounting) | Samo lozinka | Open standard (RFC) |
| **TACACS+** | TCP | 49 | Cijeli paket | Cisco proprietary |

### Kada koristiti koji?
| Slučaj | Preporuka |
| :--- | :--- |
| Upravljanje mrežnim uređajima (CLI pristup) | **TACACS+** — granularna autorizacija po naredbama |
| VPN / WiFi autentifikacija korisnika | **RADIUS** — brži, širi standard |

---

## Konfiguracija

### Lokalna AAA (bez vanjskog servera)
```bash
# Uključi AAA
Router(config)# aaa new-model

# Kreiraj lokalnog korisnika
Router(config)# username admin privilege 15 secret AdminLozinka!

# Definiraj autentifikacijsku metodu: prvo lokalna baza, backup je enable lozinka
Router(config)# aaa authentication login METODA_LOGIN local enable

# Primijeni na konzolnu liniju i VTY
Router(config)# line console 0
Router(config-line)# login authentication METODA_LOGIN

Router(config)# line vty 0 15
Router(config-line)# login authentication METODA_LOGIN
```

### RADIUS server
```bash
Router(config)# aaa new-model

# Definiraj RADIUS server
Router(config)# radius server MOJE_RADIUS
Router(config-radius-server)# address ipv4 192.168.1.200 auth-port 1812 acct-port 1813
Router(config-radius-server)# key TajniKljuc123

# Definiraj metodu autentifikacije: prvo RADIUS, backup lokalna baza
Router(config)# aaa authentication login METODA_LOGIN group radius local

# Primijeni na VTY linije
Router(config)# line vty 0 15
Router(config-line)# login authentication METODA_LOGIN
```

### TACACS+ server
```bash
Router(config)# aaa new-model

# Definiraj TACACS+ server
Router(config)# tacacs server MOJE_TACACS
Router(config-server-tacacs)# address ipv4 192.168.1.201
Router(config-server-tacacs)# key TajniKljuc456

# Autentifikacija
Router(config)# aaa authentication login METODA_LOGIN group tacacs+ local

# Autorizacija naredbi (TACACS+ specifičnost)
Router(config)# aaa authorization exec METODA_EXEC group tacacs+ local
Router(config)# aaa authorization commands 15 METODA_CMD group tacacs+ local

# Obračun (Accounting)
Router(config)# aaa accounting exec default start-stop group tacacs+
Router(config)# aaa accounting commands 15 default start-stop group tacacs+
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show aaa servers` | Status svih konfiguriranih AAA servera |
| `show tacacs` | Detalji TACACS+ konfiguracije i statistike |
| `show radius statistics` | RADIUS statistike |
| `debug aaa authentication` | Debug autentifikacije (koristiti oprezno!) |
| `show running-config \| section aaa` | Pregled AAA konfiguracije |

---

## Napomene za ispit
- `aaa new-model` **mora biti prva** naredba — odmah zamjenjuje stare `login` metode
- **RADIUS**: UDP, enkripcija samo lozinke, open standard
- **TACACS+**: TCP, enkripcija cijelog paketa, Cisco, granularna autorizacija po naredbama
- Redosljed metoda je važan: `group tacacs+ local` = prvo TACACS+, pa lokalna baza kao backup
- Ako AAA server nije dostupan a nema backup metode — **ne možeš pristupiti uređaju**!
- `default` lista = primjenjuje se na sve linije koje nemaju specifičnu listu
