# NTP — Network Time Protocol

## Svrha
NTP sinkronizira sat (sistemsko vrijeme) na svim mrežnim uređajima. Točno vrijeme kritično je za:
- **Syslog** — bez sinkroniziranog vremena logovi sa više uređaja nisu usporedivi
- **Certifikate** — SSL/TLS certifikati imaju rok trajanja
- **Kerberos autentifikaciju** — tolerira maksimalno 5 minuta razlike

---

## OSI sloj
Radi na **Application Layeru (Layer 7)**.
Koristi **UDP port 123**.

---

## Konfiguracija

### 1. NTP klijent — sinkronizacija s vanjskim serverom
```bash
# Postavi NTP server 
Router(config)# ntp server 216.239.35.0

# Postavi vremensku zonu
Router(config)# clock timezone CET 1
Router(config)# clock summer-time CEST recurring last Sun Mar 2:00 last Sun Oct 3:00
```

### 2. Ručno postavljanje vremena
```bash
# Postavljanje sistemskog vremena ručno (privileged EXEC mode)
Router# clock set 14:30:00 23 March 2026
#           HH:MM:SS  DD  Month  YYYY
```

### 3. Hardverski sat vs. Softverski sat

Cisco uređaji imaju **dva sata**:

| Sat | Naziv | Opis |
| :--- | :--- | :--- |
| **Hardware clock** | Calendar | Baterijski napajan, radi i kad je uređaj isključen |
| **Software clock** | System clock | Aktivni sat koji uređaj koristi za sve operacije |

> Kad se uređaj pokrene, softverski sat inicijalizira se iz hardverskog sata.

### Sinkronizacija između satova
```bash
# Kopiraj softverski sat → hardverski sat
Router# clock update-calendar

# Kopiraj hardverski sat → softverski sat
Router# clock read-calendar

# Provjeri hardverski sat
Router# show calendar
```

### 4. Postavljanje vremenske zone i ljetnog računanja vremena
```bash
# Postavljanje vremenske zone (CET = UTC+1)
Router(config)# clock timezone CET 1

# Automatski prijelaz na ljetno računanje (CEST = UTC+2)
# Zadnja nedjelja u ožujku u 2:00 → zadnja nedjelja u listopadu u 3:00
Router(config)# clock summer-time CEST recurring last Sun Mar 2:00 last Sun Oct 3:00
```

| Parametar | Objašnjenje |
| :--- | :--- |
| `CET` | Naziv zone (može biti bilo koji string) |
| `1` | Pomak od UTC u satima |
| `CEST` | Naziv ljetnog računanja |
| `recurring` | Automatski svake godine |

### 5. NTP master — router postaje NTP server za lokalne uređaje
```bash
# Stratum vrijednost (1-15), obično 3-5 za interne servere
Router(config)# ntp master 3
```

### 6. NTP autentifikacija (sigurnost)
```bash
Router(config)# ntp authenticate
Router(config)# ntp authentication-key 1 md5 CiscoNTP123
Router(config)# ntp trusted-key 1
Router(config)# ntp server 10.0.0.1 key 1
```

---

## Verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show clock` | Trenutno sistemsko (softverski) vrijeme na uređaju |
| `show clock detail` | Vrijeme + izvor (NTP, hardware, ručno) |
| `show calendar` | Hardverski sat (calendar) |
| `show ntp status` | Status sinkronizacije, stratum, referentni server |
| `show ntp associations` | Popis svih NTP servera i njihov status |

**Primjer ispravne sinkronizacije:**
```
Router# show ntp status
Clock is synchronized, stratum 4, reference is 10.0.0.1
```
> Ključna riječ: **synchronized** — znači da je uređaj uspješno sinkroniziran.

---

## Napomene za ispit 
- NTP koristi **UDP 123**
- **Stratum 0** = atomski sat / GPS (referentni izvor, ne šalje NTP pakete direktno)
- **Stratum 1** = server direktno spojen na Stratum 0 izvor
- **Stratum 15** = maksimum, Stratum 16 = unsynchronized
- `ntp master` čini router NTP serverom čak i bez vanjske sinkronizacije
- Cisco uređaji mogu biti **klijent i server** istovremeno
- **Hardware clock** (calendar) = baterijski napajan, opstaje i nakon gašenja uređaja
- **Software clock** = aktivni sat; inicijalizira se iz hardware clocka pri pokretanju
- `clock update-calendar` — softverski → hardverski; `clock read-calendar` — hardverski → softverski
- `clock set` se izvršava u **privileged EXEC** modu (bez `config`)
- `clock timezone` i `clock summer-time` se postavljaju u **global config** modu

