# 05 - DHCP Snooping

## Svrha
DHCP Snooping štiti mrežu od **lažnog DHCP servera (Rogue DHCP Server)**. Napadač može postaviti vlastiti DHCP server i korisnicima dodijeliti lažnu konfiguraciju (npr. sebe kao default gateway) — tzv. **Man-in-the-Middle napad**.

DHCP Snooping svrstava switcheve portove u dvije kategorije:
- **Trusted (pouzdani)** — port prema legitimnom DHCP serveru ili upstream switchu
- **Untrusted (nepouzdani)** — port prema krajnjim korisnicima (default za sve portove)


---

## OSI sloj
Radi na **Data Link Layeru (Layer 2)**.

---

## Kako radi

```
Klijent ← [Untrusted port] ← Switch → [Trusted port] → Legitiman DHCP server

Ako DHCP Offer stigne na Untrusted port → Switch blokira paket
Ako DHCP Offer stigne na Trusted port → Switch propušta paket
```

Switch gradi **DHCP Snooping Binding tablicu** koja pamti:
`MAC adresa | IP adresa | VLAN | Port | Lease time`

Ova tablica se koristi i od strane **DAI** (Dynamic ARP Inspection).

---

## Konfiguracija

```bash
# Korak 1: Uključi DHCP Snooping globalno
Switch(config)# ip dhcp snooping

# Korak 2: Uključi za specifični VLAN
Switch(config)# ip dhcp snooping vlan 10
Switch(config)# ip dhcp snooping vlan 10,20,30        # Više VLAN-ova

# Korak 3: Postavi trusted port (prema DHCP serveru ili upstream switchu)
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip dhcp snooping trust

# Korak 4: (Opcionalno) Ograniči brzinu DHCP paketa na untrusted portu
# Sprječava DHCP starvation napad
Switch(config)# interface FastEthernet0/1
Switch(config-if)# ip dhcp snooping limit rate 15     # max 15 DHCP paketa/sekundi

# Korak 5: Isključi option 82 (ako uzrokuje probleme s upstream DHCP serverom)
Switch(config)# no ip dhcp snooping information option
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ip dhcp snooping` | Globalni status, trusted portovi, VLAN-ovi |
| `show ip dhcp snooping binding` | DHCP Snooping Binding tablica (MAC, IP, port, VLAN) |
| `show ip dhcp snooping statistics` | Statistike — broj odbijenih paketa |

**Primjer Binding tablice:**
```
Switch# show ip dhcp snooping binding
MacAddress          IpAddress       Lease(sec) Type      VLAN  Interface
------------------  ---------------  ---------- --------  ----  ----------------
AA:BB:CC:11:22:33   192.168.10.50   86400      dynamic   10    FastEthernet0/5
```

---

## Napomene za ispit
- **Svi portovi su Untrusted po defaultu** — eksplicitno postavi trusted samo portove prema DHCP serveru/uplinku
- DHCP Snooping gradi **Binding tablicu** — koriste je i DAI i IP Source Guard
- **Option 82** (DHCP relay information) — može uzrokovati probleme; isključi s `no ip dhcp snooping information option`
- DHCP Snooping štiti od: **Rogue DHCP servera** i **DHCP Starvation napada**
- Mora biti uključen per-VLAN: `ip dhcp snooping vlan [id]`
