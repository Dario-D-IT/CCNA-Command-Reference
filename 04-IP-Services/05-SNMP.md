# SNMP — Simple Network Management Protocol

## Svrha
SNMP omogućuje **centralizirano praćenje i upravljanje** mrežnim uređajima (routeri, switchevi, serveri). NMS (Network Management Station) može čitati statistike i konfigurirati uređaje remotely.


---

## OSI sloj
Radi na **Application Layeru (Layer 7)**.
- **UDP 161** — za SNMP upite (NMS → Agent)
- **UDP 162** — za SNMP trapove (Agent → NMS)

---

## Komponente SNMP arhitekture

| Komponenta | Opis |
| :--- | :--- |
| **NMS** (Network Management Station) | Centralni server koji upravlja uređajima (npr. SolarWinds, PRTG) |
| **SNMP Agent** | Softver koji radi na svakom Cisco uređaju |
| **MIB** (Management Information Base) | Baza podataka varijabli koje se mogu pratiti (npr. CPU, sučelja) |
| **OID** (Object Identifier) | Jedinstveni identifikator svake varijable u MIB-u |

---

## SNMP verzije

| Verzija | Sigurnost | Napomena |
| :--- | :--- | :--- |
| **SNMPv1** | Community string (plaintext) | Zastarjelo |
| **SNMPv2c** | Community string (plaintext) | Najčešće korišten (CCNA fokus) |
| **SNMPv3** | Autentifikacija + enkripcija | Best practice, jedino sigurno |

---

## SNMP operacije

| Operacija | Smjer | Opis |
| :--- | :--- | :--- |
| **GET** | NMS → Agent | Čitanje jedne varijable |
| **GET-NEXT** | NMS → Agent | Čitanje sljedeće varijable u MIB-u |
| **GET-BULK** | NMS → Agent | Čitanje više varijabli odjednom (v2c+) |
| **SET** | NMS → Agent | Postavljanje/promjena vrijednosti |
| **TRAP** | Agent → NMS | Spontana obavijest o događaju (nesolicited) |
| **INFORM** | Agent → NMS | Kao TRAP, ali s potvrdom isporuke (v2c+) |

---

## Konfiguracija

### SNMPv2c (read-only i read-write)
```bash
# Read-only pristup (NMS može samo čitati)
Router(config)# snmp-server community JAVNA_LOZINKA ro

# Read-write pristup (NMS može čitati i mijenjati konfiguraciju)
Router(config)# snmp-server community PRIVATNA_LOZINKA rw

# Postavi lokaciju i kontakt (metapodaci o uređaju)
Router(config)# snmp-server location "Server Room, Rack 3"
Router(config)# snmp-server contact "admin@tvrtka.hr"

# Konfiguracija TRAP-ova — šalji obavijesti na NMS server
Router(config)# snmp-server host 192.168.1.100 version 2c JAVNA_LOZINKA
Router(config)# snmp-server enable traps
```

### SNMPv3 (sigurna opcija)
```bash
# Kreiranje SNMP grupe s autentifikacijom i enkripcijom
Router(config)# snmp-server group ADMIN_GRUPA v3 priv

# Kreiranje SNMP korisnika
Router(config)# snmp-server user admin ADMIN_GRUPA v3 auth sha Lozinka123 priv aes 128 Enkripcija456
```

---

## Verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show snmp` | Globalni SNMP status, statistike paketa |
| `show snmp community` | Konfigurirane community stringove |
| `show snmp host` | Konfigurirani NMS hostovi (trap odredišta) |
| `show snmp user` | SNMPv3 korisnici |
| `show snmp group` | SNMPv3 grupe |

---

## Napomene za ispit
- **UDP 161** = upiti (GET/SET), **UDP 162** = trapovi
- **SNMPv1/v2c** koriste **community string** (nešifrirano — sigurnosni rizik!)
- **SNMPv3** jedini ima **autentifikaciju + enkripciju**
- **TRAP** = uređaj sam šalje obavijest, bez potvrde
- **INFORM** = kao TRAP, ali NMS potvrđuje prijem
- Read-only community: NMS može samo **čitati** MIB varijable
- Read-write community: NMS može i **mijenjati** konfiguraciju uređaja
