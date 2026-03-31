# 04 - Port Security

## Svrha
Port Security ograničava **koje MAC adrese smiju komunicirati** na switch portu. Zaštita od:
- Neovlaštenog spajanja uređaja
- **MAC flooding napada** — napadač šalje tisuće lažnih MAC adresa kako bi prepunio CAM tablicu switcha
- Neovlaštenog spajanja neovlaštenih switcheva ili access pointova


---

## OSI sloj
Radi na **Data Link Layeru (Layer 2)**.

---

## Načini učenja MAC adresa

| Način | Opis |
| :--- | :--- |
| **Static** | Ručno unosimo dozvoljenu MAC adresu |
| **Dynamic** | Switch sam uči MAC adresu, ali je ne sprema u konfiguraciju (briše se pri restartu) |
| **Sticky** | Switch sam uči MAC adresu i **automatski je sprema u running-config, ali treba je spremiti i u startup-config** |

---

## Akcije pri kršenju (Violation Modes)

| Mod | Promet se blokira? | Port se gasi? | Log poruka? | SNMP trap? |
| :--- | :---: | :---: | :---: | :---: |
| **Protect** | Da | Ne | Ne | Ne |
| **Restrict** | Da | Ne | Da | Da |
| **Shutdown** | Da | Da (err-disabled) | Da | Da |

> **Shutdown je default** i preporučeni mod za produkciju.

---

## Konfiguracija

> ⚠️ Port Security radi **samo na access portovima** — sučelje mora biti u access modu!

```bash
# Korak 1: Postavi port u access mod
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access

# Korak 2: Uključi Port Security
Switch(config-if)# switchport port-security

# Korak 3: Postavi maksimalan broj MAC adresa (default = 1)
Switch(config-if)# switchport port-security maximum 2

# Korak 4: Odaberi način učenja MAC adresa

# Opcija A: Statički (ručno)
Switch(config-if)# switchport port-security mac-address 00AA.BBCC.DD11

# Opcija B: Sticky (automatsko učenje + spremanje)
Switch(config-if)# switchport port-security mac-address sticky

# Korak 5: Postavi violation mod
Switch(config-if)# switchport port-security violation shutdown   # default
Switch(config-if)# switchport port-security violation restrict
Switch(config-if)# switchport port-security violation protect
```

### Oporavak err-disabled porta
```bash
# Ručni oporavak (isključi i uključi port)
Switch(config)# interface FastEthernet0/1
Switch(config-if)# shutdown
Switch(config-if)# no shutdown

# Automatski oporavak (vraća port nakon određenog vremena)
Switch(config)# errdisable recovery cause psecure-violation
Switch(config)# errdisable recovery interval 300       # Nakon 300 sekundi
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show port-security` | Pregled svih portova s Port Security i statistike kršenja |
| `show port-security interface FastEthernet0/1` | Detalji Port Security na specifičnom portu |
| `show port-security address` | Popis svih naučenih/statičkih MAC adresa |
| `show interfaces FastEthernet0/1 status` | Provjera je li port u err-disabled stanju |

**Primjer ispravnog outputa:**
```
Switch# show port-security interface Fa0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Maximum MAC Addresses      : 2
Total MAC Addresses        : 1
Sticky MAC Addresses       : 1
Security Violation Count   : 0
```

---

## Napomene za ispit
- Port Security radi **samo na access portovima** (ne na trunk portovima)
- Default: maksimalno **1 MAC adresa**, violation mod = **Shutdown**
- **Sticky** = uči dinamički + sprema u konfiguraciju (kombinacija static + dynamic)
- **Err-disabled** stanje = port je potpuno ugašen zbog kršenja sigurnosti
- Za oporavak err-disabled porta: `shutdown` → `no shutdown`
- `show port-security` prikazuje broj kršenja — korisno za troubleshooting
