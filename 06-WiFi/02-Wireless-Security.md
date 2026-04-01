# 02 - Bežična sigurnost

## Svrha
Zaštita bežičnih mreža od neovlaštenog pristupa i prisluškivanja prometa. Za razliku od žičnih mreža, bežični signal fizički nije ograničen — svako u dometu može "čuti" promet.

---

## OSI sloj
Autentifikacija i enkripcija rade na **Data Link Layeru (Layer 2)** i **Application Layeru (Layer 7)**.

---

## 1. Evolucija bežičnih sigurnosnih protokola

| Protokol | Godina | Enkripcija | Status | Problem |
| :--- | :--- | :--- | :--- | :--- |
| **WEP** | 1997 | RC4 (40/104-bit) | Zastarjelo  | Lako se probije |
| **WPA** | 2003 | TKIP (RC4) | Zastarjelo  | TKIP je "zakrpa" za WEP |
| **WPA2** | 2004 | AES-CCMP (128-bit) | Trenutni standard ✓ | . |
| **WPA3** | 2018 | AES-GCMP (256-bit) | Najnoviji ✓ | . |

---

## 2. Personal vs Enterprise načini

### Personal (PSK — Pre-Shared Key)
- Svi korisnici dijele **istu lozinku**
- Jednostavno postavljanje — za domove i manje firme
- Nema centralnog upravljanja korisnicima

```
Klijent → [WPA2/WPA3 + PSK] → AP
```

### Enterprise (802.1X + EAP + RADIUS)
- Svaki korisnik ima **vlastite kredencijale**
- Autentifikacija putem **RADIUS servera**
- Koristi **EAP (Extensible Authentication Protocol)** kao okvir
- Za firme i obrazovne ustanove

```
Klijent → [EAP] → AP (Authenticator) → [RADIUS] → RADIUS Server (Authentication Server)
```

---

## 3. WPA2 Personal — 4-Way Handshake

Nakon što klijent pošalje ispravnu lozinku (PSK), odvija se 4-smjerni rukovanje za generiranje ključeva sesije:

```
1. AP  → Klijent : ANonce (slučajni broj)
2. Klijent → AP  : SNonce + MIC (Message Integrity Code)
3. AP  → Klijent : GTK (Group Temporal Key) + MIC
4. Klijent → AP  : Potvrda (ACK)
```

---

## 4. EAP metode (Enterprise)

| EAP metoda | Autentifikacija klijenta | Autentifikacija servera | Napomena |
| :--- | :--- | :--- | :--- |
| **EAP-TLS** | Certifikat | Certifikat | Najsigurniji, obostrani certifikati |
| **PEAP** | Username/password | Certifikat servera | Čest u firmama |
| **EAP-TTLS** | Username/password | Certifikat servera | Sličan PEAP-u |
| **EAP-FAST** | PAC token | PAC token | Cisco, bez certifikata |

---

## 5. Dodatni sigurnosni mehanizmi

```
# Sakrivanje SSID-a (security through obscurity — samo ovo nije dovoljno!)
# Konfigurira se na AP-u / WLC-u u GUI-ju

# MAC filtriranje (slaba zaštita — MAC se može lažirati)
# Whitelist dozvoljenih MAC adresa na AP-u
```

### Napadi na bežične mreže (za razumijevanje)

| Napad | Opis |
| :--- | :--- |
| **Evil Twin** | Lažni AP s istim SSID-om — klijenti se spajaju na napadača |
| **Deauthentication** | Slanje lažnih deauth okvira — forsiranje ponovnog spajanja |
| **KRACK** | Napad na WPA2 4-Way Handshake (zakrpan softverskim updateom) |
| **Dictionary napad** | Probijanje WPA2 PSK lozinke pogađanjem |

---

## Verifikacija (na WLC GUI ili show naredbe)

| Naredba | Svrha |
| :--- | :--- |
| `show wlan summary` | Popis konfiguriranih WLAN-ova i sigurnosnih profila |
| `show wlan id [id]` | Detalji specifičnog WLAN-a (security policy, enkripcija) |
| `show client summary` | Popis spojenih klijenata i njihov status autentifikacije |

---

## Napomene za ispit
- **WEP = zastarjelo i nesigurno** — nikad ne koristiti
- **WPA2-Personal** = AES + PSK (zajednička lozinka)
- **WPA2-Enterprise** = AES + 802.1X + RADIUS (individualni kredencijali)
- **WPA3** zamjenjuje PSK s **SAE (Simultaneous Authentication of Equals)** — otpornost na dictionary napade
- **CCMP** (Counter Mode CBC-MAC Protocol) je enkripcijski protokol unutar WPA2 — koristi **AES**
- **TKIP** (WPA) je zastarjeo i ne smije se koristiti u novim instalacijama
- Sakrivanje SSID-a i MAC filtriranje **nisu dovoljni** sigurnosni mehanizmi
