# Syslog — Centralizirano upravljanje logovima

## Svrha
Syslog je standardni protokol za **slanje log poruka** s mrežnih uređaja na centralizirani Syslog server. Omogućuje praćenje događaja, grešaka i promjena konfiguracije na svim uređajima s jednog mjesta.


---

## OSI sloj
Radi na **Application Layeru (Layer 7)**.
Koristi **UDP port 514** (defaultno) ili TCP 514 / TCP 6514 (TLS).

---

## Severity razine (8 razina)

| Razina | Naziv | Opis | Pamti |
| :---: | :--- | :--- | :--- |
| 0 | Emergency | Sustav je neupotrebljiv | **E**very |
| 1 | Alert | Hitna akcija potrebna | **A**ction |
| 2 | Critical | Kritična stanja | **C**auses |
| 3 | Error | Greške | **E**rrors |
| 4 | Warning | Upozorenja | **W**hen |
| 5 | Notice | Normalni ali važni događaji | **N**etwork |
| 6 | Informational | Informativne poruke | **I**nfo |
| 7 | Debug | Debug poruke (detaljno) | **D**umps |



> ⚠️ Kada postaviš razinu, Syslog šalje **tu razinu i sve više prioritetne** (manji broj = viši prioritet). Npr. `logging trap 4` šalje razine 0-4.

---

## Konfiguracija

```bash
# Postavi Syslog server (IP adresa centralnog servera)
Router(config)# logging host 192.168.1.100

# Postavi severity razinu koja se šalje na server (0-7)
Router(config)# logging trap warnings       # šalje razine 0-4
Router(config)# logging trap 4              # isto kao iznad

# Uključi timestamp u log porukama (važno za troubleshooting!)
Router(config)# service timestamps log datetime msec

# Postavi severity razinu za konzolu (0-7)
Router(config)# logging console 7           # sve poruke

# Postavi severity razinu za VTY linije (buffer)
Router(config)# logging buffered 6          # informational i više

# Veličina buffer-a (u bajtovima)
Router(config)# logging buffered 16384

# Sinkronizacija logova s konzolom (sprječava prekid unosa komandi)
Router(config-line)# logging synchronous
```

---

## Verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show logging` | Status logginga, buffer sadržaj, postavke |
| `show logging | include ERROR` | Filtriranje samo ERROR poruka |
| `terminal monitor` | Prikaz log poruka u SSH/Telnet sesiji |
| `terminal no monitor` | Isključivanje prikaza logova u sesiji |

---

## Napomene za ispit
- Syslog koristi **UDP 514** (bez potvrde isporuke!)
- **8 razina** (0-7), niži broj = viši prioritet
- `logging trap` = razina za **vanjski server**
- `logging console` = razina za **konzolni port**
- `logging buffered` = razina za **interni RAM buffer**
- `terminal monitor` je potreban za prikaz logova u **remote (SSH/Telnet) sesiji**
- `service timestamps` je **best practice** — uvijek uključiti
