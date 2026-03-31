# 01 - Osiguravanje mrežnih uređaja

## Svrha
Zaštita fizičkog i logičkog pristupa Cisco uređajima — routerima i switchevima. Cilj je spriječiti neovlašteni pristup konzoli, remote sesijama i privilegiranom načinu rada.

---

## OSI sloj
Konfiguracija sigurnosti radi na više slojeva:
- **Layer 7 (Application)** — SSH, Telnet, autentifikacija
- **Layer 2 (Data Link)** — fizički pristup konzoli

---

## 1. Lozinke i pristup konzoli

```bash
# Lozinka za ulaz u privilegirani način (enable mode)
# UVIJEK koristi "secret" (MD5 hash) umjesto "password" (plaintext)!
Router(config)# enable secret MojaLozinka123

# Lozinka za konzolni port
Router(config)# line console 0
Router(config-line)# password KonzolnaLozinka
Router(config-line)# login
Router(config-line)# exec-timeout 5 0          # Odjava nakon 5 min neaktivnosti

# Enkripcija svih lozinki u konfiguraciji (slaba enkripcija, ali bolje od ničeg)
Router(config)# service password-encryption
```

---

## 2. SSH — Siguran remote pristup

```bash
# Korak 1: Postavi hostname i domain name (obavezno za generiranje ključa)
Router(config)# hostname R1
R1(config)# ip domain-name tvrtka.hr

# Korak 2: Generiraj RSA ključ (preporučeno: 2048 bita)
R1(config)# crypto key generate rsa modulus 2048

# Korak 3: Kreiraj lokalnog korisnika
R1(config)# username admin privilege 15 secret AdminLozinka!

# Korak 4: Postavi VTY linije za SSH (onemogući Telnet)
R1(config)# line vty 0 15
R1(config-line)# transport input ssh           # Samo SSH, bez Telneta
R1(config-line)# login local                  # Koristi lokalnu bazu korisnika
R1(config-line)# exec-timeout 10 0            # Odjava nakon 10 min

# Korak 5: Postavi SSH verziju 2
R1(config)# ip ssh version 2
```

---

## 3. Banneri (poruke upozorenja)

```bash
# MOTD banner — prikazuje se SVIMA koji se pokušaju spojiti
Router(config)# banner motd #
UPOZORENJE: Neovlašteni pristup je zabranjen!
Sve aktivnosti se bilježe i nadziru.
#

# Login banner — prikazuje se prije unosa lozinke
Router(config)# banner login #
Autorizirani korisnici samo!
#
```

---

## 4. Privilege razine

```bash
# Postavljanje privilege razine korisniku (0-15, default za login = 1, enable = 15)
Router(config)# username operater privilege 5 secret OperaterLozinka

# Postavljanje lozinke za specifičnu privilege razinu
Router(config)# enable secret level 5 RazinaLozinka
```

---

## 5. Isključivanje nekorištenih servisa

```bash
# Isključi HTTP server (ako ne koristiš web upravljanje)
Router(config)# no ip http server
Router(config)# no ip http secure-server

# Isključi CDP prema Internetu / end-uređajima
Router(config-if)# no cdp enable

# Isključi nekorištena sučelja
Router(config-if)# shutdown
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show running-config` | Pregled trenutne konfiguracije (provjera lozinki i SSH) |
| `show ip ssh` | SSH status, verzija i timeout postavke |
| `show users` | Popis aktivnih sesija na uređaju |
| `show line` | Status konzolnih i VTY linija |
| `show privilege` | Trenutna privilege razina |

---

## Napomene za ispit
- `enable secret` uvijek pobjeđuje `enable password` — koristiti samo `secret`
- `service password-encryption` koristi **Type 7** (slaba, reversibilna) enkripciju
- `enable secret` koristi **Type 5** (MD5) — puno jači
- SSH zahtijeva: **hostname + domain name + RSA ključ + lokalni korisnik**
- **Telnet šalje podatke nešifrirano** — uvijek koristiti SSH!
- `transport input ssh` na VTY linijama blokira Telnet
- `exec-timeout 0 0` = nikad ne odjavljivati (sigurnosni rizik!)
