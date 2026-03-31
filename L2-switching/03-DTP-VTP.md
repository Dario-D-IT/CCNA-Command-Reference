# 03 - DTP & VTP

## Svrha
- **DTP** (Dynamic Trunking Protocol) — automatski pregovara uspostavu trunk linka između Cisco switcheva
- **VTP** (VLAN Trunking Protocol) — sinkronizira bazu VLAN-ova (vlan.dat) između switcheva u istoj domeni

---

## OSI sloj
Oba protokola rade na **Data Link Layeru (Layer 2)**.

---

## DTP — Dynamic Trunking Protocol

### DTP modovi porta

| Mod | Opis |
| :--- | :--- |
| `dynamic desirable` | Aktivno pokušava uspostaviti trunk |
| `dynamic auto` | Čeka na drugu stranu da inicira trunk (default na starijim modelima) |
| `trunk` | Forsira trunk, ali još šalje DTP okvire |
| `access` | Forsira access mod |
| `nonegotiate` | Potpuno isključuje DTP okvire |

### Konfiguracija DTP-a

```bash
# Isključivanje DTP pregovaranja na korisničkim portovima (sigurnosna praksa)
interface fastEthernet 0/1
switchport mode access
switchport nonegotiate          # Potpuno isključuje DTP okvire

# Forsiranje trunk linka bez DTP pregovaranja
interface gigabitEthernet 0/1
switchport mode trunk
switchport nonegotiate
```

> **Sigurnosna napomena:** DTP treba isključiti na svim portovima prema krajnjim uređajima kako bi se spriječio **VLAN hopping** napad.

---

## VTP — VLAN Trunking Protocol

### VTP modovi

| Mod | Može kreirati VLAN? | Propagira promjene? | Sprema u vlan.dat? |
| :--- | :---: | :---: | :---: |
| **Server** | Da | Da | Da |
| **Client** | Ne | Da | Da |
| **Transparent** | Da (lokalno) | Ne | Da |
| **Off** | Da (lokalno) | Ne | Ne |

### Konfiguracija VTP-a

```bash
# Postavljanje VTP domene
Switch(config)# vtp domain MOJA_MREZA

# Postavljanje VTP lozinke (opcionalno, ali preporučeno)
Switch(config)# vtp password CISCO123

# Postavljanje VTP moda
Switch(config)# vtp mode server
Switch(config)# vtp mode client
Switch(config)# vtp mode transparent

# Postavljanje VTP verzije
Switch(config)# vtp version 2
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show vtp status` | Prikazuje domenu, verziju, mod i revision broj |
| `show vtp password` | Prikazuje konfiguriranu VTP lozinku |
| `show vtp counters` | Prikazuje VTP statističke brojače |
| `show interfaces trunk` | Prikazuje aktivne trunk portove |

---

## Napomene za ispit
- **DTP je Cisco proprietarni** protokol — isključi ga prema end-uređajima (`nonegotiate`)
- **VTP Revision broj** je kritičan — switch s višim revision brojem prepisat će VLAN bazu svih ostalih!
- Kad dodaš novi switch u mrežu: postavi ga u **Transparent** mod ili resetiraj revision broj
- VTP Transparent switch **ne primjenjuje** VTP promjene, ali ih **prosljeđuje** dalje
- `vtp mode off` potpuno onemogućuje VTP (samo na VTP verziji 3)
- Sigurnosna preporuka: koristiti **VTP Transparent** ili **Off** mod — izbjegavati automatsku sinkronizaciju
