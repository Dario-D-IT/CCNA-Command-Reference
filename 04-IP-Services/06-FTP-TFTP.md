# FTP & TFTP — Protokoli za prijenos datoteka

## Svrha
Koriste se za **prijenos datoteka** na mrežnim uređajima — najčešće za:
- Upload/download **IOS firmware** na Cisco uređaje
- **Backup konfiguracije** (running-config, startup-config)
- Prijenos log datoteka

---

## OSI sloj
Radi na **Application Layeru (Layer 7)**.

| Protokol | Transport | Portovi |
| :--- | :--- | :--- |
| **FTP** | TCP | 20 (data), 21 (control) |
| **TFTP** | UDP | 69 |
| **SFTP** | TCP (SSH) | 22 |
| **SCP** | TCP (SSH) | 22 |

---

## Usporedba FTP vs TFTP

| Karakteristika | FTP | TFTP |
| :--- | :--- | :--- |
| Transport | TCP (pouzdan) | UDP (nepouzdan) |
| Autentifikacija | Da (username/password) | Ne |
| Sigurnost | Nešifrirano (plaintext) | Nešifrirano |
| Veličina datoteka | Neograničeno | Ograničeno |
| Složenost | Viša | Minimalna |
| Tipična upotreba | Backup konfiguracija | IOS upgrade, boot |
| Cisco upotreba | Česta | Najčešća za IOS |

---

## Konfiguracija

### Kopiranje IOS-a putem TFTP
```bash
# Kopiranje IOS image-a s TFTP servera na flash
Router# copy tftp flash
Address or name of remote host []? 192.168.1.100
Source filename []? c2900-universalk9-mz.SPA.155-3.M4a.bin
Destination filename [c2900-universalk9-mz.SPA.155-3.M4a.bin]?

# Kopiranje running-config na TFTP server (backup)
Router# copy running-config tftp
Address or name of remote host []? 192.168.1.100
Destination filename [router-confg]? backup-config.txt

# Kopiranje s TFTP na running-config (restore)
Router# copy tftp running-config
```

### Kopiranje putem FTP
```bash
# Postavi FTP kredencijale
Router(config)# ip ftp username admin
Router(config)# ip ftp password lozinka123

# Kopiranje IOS-a s FTP servera
Router# copy ftp flash
Address or name of remote host []? 192.168.1.100
Source filename []? /ios/c2900-universalk9-mz.bin
```

### Postavljanje boot image (nakon uploada novog IOS-a)
```bash
# Postavi koji IOS se koristi pri ponovnom pokretanju
Router(config)# boot system flash c2900-universalk9-mz.SPA.155-3.M4a.bin

# Provjeri i spremi
Router# write memory
Router# reload
```

---

## Verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show flash:` | Sadržaj flash memorije (IOS datoteke) |
| `show flash: all` | Detalji flash-a s veličinama |
| `show version` | Trenutni IOS, uptime, hardware informacije |
| `show boot` | Konfigurirani boot image |
| `dir flash:` | Popis datoteka u flash-u |

**Provjera slobodnog mjesta prije uploada:**
```
Router# show flash:
-#- --length-- -----date/time------ path
1   55882028   Mar 22 2026 10:00:00  c2900-universalk9-mz.SPA.155-3.M4a.bin

63881216 bytes available (55882028 bytes used)
```

---

## Napomene za ispit
- **TFTP** = UDP 69, bez autentifikacije, jednostavan, najčešći za IOS upgrade
- **FTP** = TCP 20/21, s autentifikacijom, pouzdan prijenos
- Oba protokola su **nešifrirana** — za sigurnost koristi SCP (SSH)
- `copy running-config startup-config` = lokalni backup (ne koristi FTP/TFTP)
- `copy tftp flash` = upload IOS-a na uređaj
- `copy flash tftp` = download IOS-a s uređaja (backup)
- Uvijek provjeri slobodnu flash memoriju **prije** uploada novog IOS-a
