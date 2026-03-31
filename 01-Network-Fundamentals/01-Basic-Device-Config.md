# 01 - Osnovna konfiguracija uređaja

## Svrha
Postavljanje osnovnih sigurnosnih i identifikacijskih postavki na svim Cisco uređajima — routerima i switchevima.

---

## OSI sloj
Konfiguracija se odnosi na **sve slojeve** — od fizičkog pristupa (konzola) do aplikacijskog (SSH, management).

---

## 1. Pristup uređaju i načini rada

```
Korisnički način   → Router>          (ograničen pristup, samo show naredbe)
Privilegirani način → Router#          (puni pristup, enable)
Globalna konfiguracija → Router(config)#  (konfiguracija uređaja)
Interface konfiguracija → Router(config-if)#
Line konfiguracija → Router(config-line)#
```

```bash
Router> enable                    # Ulazak u privilegirani način
Router# configure terminal        # Ulazak u globalnu konfiguraciju
Router(config)# exit              # Izlaz jedan korak nazad
Router(config)# end               # Izlaz direktno u privilegirani način
Router# disable                   # Povratak u korisnički način
```

---

## 2. Osnovna konfiguracija

```bash
# Postavljanje hostnamea
Router(config)# hostname R1

# Onemogući DNS lookup (sprječava čekanje pri krivim naredbama)
R1(config)# no ip domain-lookup

# Sinhronizirani log (sprječava prekidanje tipkanja log porukama)
R1(config)# line console 0
R1(config-line)# logging synchronous

# Timeout neaktivnosti (odjava nakon 10 minuta)
R1(config-line)# exec-timeout 10 0
```

---

## 3. Lozinke

```bash
# Lozinka za privilegirani način (uvijek koristi SECRET!)
R1(config)# enable secret JakaLozinka!

# Lozinka za konzolni pristup
R1(config)# line console 0
R1(config-line)# password KonzolnaLoz!
R1(config-line)# login

# Lozinka za Telnet/SSH (VTY linije)
R1(config)# line vty 0 15
R1(config-line)# password VTYLozinka!
R1(config-line)# login

# Enkripcija svih plaintext lozinki u konfiguraciji
R1(config)# service password-encryption
```

---

## 4. SSH pristup

```bash
# Preduvjeti za SSH
R1(config)# hostname R1                          # Mora biti postavljen
R1(config)# ip domain-name tvrtka.hr             # Mora biti postavljen
R1(config)# crypto key generate rsa modulus 2048 # Generiraj RSA ključ
R1(config)# ip ssh version 2                     # Koristi samo SSH v2
R1(config)# username admin privilege 15 secret AdminLoz!

# Postavi VTY linije — samo SSH, bez Telneta
R1(config)# line vty 0 15
R1(config-line)# transport input ssh
R1(config-line)# login local
```

---

## 5. Banneri

```bash
# MOTD — prikazuje se svima (upozorenje o neovlaštenom pristupu)
R1(config)# banner motd #
UPOZORENJE: Pristup ovom uređaju dozvoljen je samo ovlaštenim osobama!
#

# Login banner — prikazuje se prije unosa lozinke
R1(config)# banner login #
Autorizirani korisnici samo. Sve aktivnosti se bilježe.
#
```

---

## 6. Konfiguracija sučelja (Interface)

```bash
# Router sučelje
R1(config)# interface GigabitEthernet0/0
R1(config-if)# description Veza prema Switchu SW1
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown                      # Fizički uključi sučelje

# Switch Management SVI (za SSH/Telnet pristup switchu)
SW1(config)# interface vlan 1
SW1(config-if)# ip address 192.168.1.10 255.255.255.0
SW1(config-if)# no shutdown
SW1(config)# ip default-gateway 192.168.1.1    # Default gateway za switch
```

---

## 7. Spremanje konfiguracije

```bash
# Spremi running-config u startup-config
R1# write memory
# ILI
R1# copy running-config startup-config

# Provjera
R1# show startup-config
R1# show running-config
```

---

## 8. Korisne show naredbe

| Naredba | Svrha |
| :--- | :--- |
| `show version` | IOS verzija, uptime, hardware, serijski broj |
| `show running-config` | Trenutna konfiguracija u RAM-u |
| `show startup-config` | Konfiguracija u NVRAM-u (pri pokretanju) |
| `show ip interface brief` | Kratak pregled sučelja — IP, status, protokol |
| `show interfaces` | Detalji svih sučelja |
| `show ip route` | Routing tablica |
| `show mac address-table` | CAM tablica (switch) |
| `show cdp neighbors` | Direktno spojeni Cisco susjedi |

---

## Napomene za ispit 
- `enable secret` > `enable password` — uvijek koristiti `secret` (MD5)
- `service password-encryption` = **Type 7** — slaba enkripcija, ali bolje od ništa
- SSH zahtijeva: **hostname + domain name + RSA ključ**
- `no shutdown` je obavezno — sučelja su po defaultu **administratively down**
- `write memory` = spremi konfiguraciju (ne sprema se automatski!)
- **Running-config** = RAM (izgubi se pri restartU); **Startup-config** = NVRAM (ostaje)
- `exec-timeout 0 0` = nikad ne odjavljivati — **sigurnosni rizik!**
