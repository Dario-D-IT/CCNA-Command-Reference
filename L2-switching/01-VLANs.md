# 01 - VLANs i Trunking (802.1Q)

**Pojam:** VLAN-ovi (Virtual Local Area Networks) omogućuju logičku segmentaciju fizičkog switcha u više broadcast domena, čime se poboljšavaju sigurnost, performanse i upravljanje mrežom.

**Broadcast domena** je grupa uređaja koji će primiti broadcast frame (odredišna MAC: FFFF.FFFF.FFFF) koji pošalje bilo koji od njezinih članova.

---

## Konfiguracija: Access i Trunk portovi

### 1. Kreiranje i imenovanje VLAN-a
```bash
vlan 10
name PRODAJA
```

### 2. Postavljanje Access porta
```bash
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
```

### 3. Konfiguracija Trunk linka
```bash
interface gigabitEthernet 0/1
switchport trunk encapsulation dot1q
switchport mode trunk
```

### 4. VLAN rasponi
VLAN-ovi su podijeljeni u dvije grupe:
```
Normalni VLAN-ovi:  1 – 1005
Prošireni VLAN-ovi: 1006 – 4094
```

VLAN-ovi koji postoje na switchu po defaultu:
* 1, 1002, 1003, 1004, 1005

### 5. Zanimljivi VLAN-ovi
Potrebno je shvatiti razliku između:

##### 1. Default VLAN (VLAN 1)
* Tvornički definiran VLAN u koji pripadaju svi portovi switcha po defaultu.
* Radi na L2 sloju.
* Ne možeš ga obrisati niti mu promijeniti ime. Preko njega idu kontrolni protokoli poput CDP, LLDP, VTP i STP.
* Preporuka: ne koristiti ga za podatkovni promet.

##### 2. Native VLAN
Poseban VLAN namijenjen isključivo **Trunk** portovima. Služi za prijenos prometa koji nema 802.1Q tag (tzv. untagged traffic).
Radi na L2 sloju. Na oba kraja trunk linka mora biti isti Native VLAN. Po defaultu je VLAN 1 — preporuka je promijeniti ga.

##### 3. Management VLAN
VLAN kojemu dodjeljuješ IP adresu putem **SVI (Switch Virtual Interface)** kako bi se mogao spojiti na switch putem SSH/Telneta.

### 6. Trunking protokol
Trunking omogućuje prijenos prometa iz više različitih VLAN-ova preko jedne fizičke veze između dva mrežna uređaja (obično dva switcha ili switcha i routera).

##### 1. Trunking protokoli
Postoje dva glavna načina kako switch označava (tagira) okvire da bi znao kojem VLAN-u pripadaju:
* **ISL** (Inter-Switch Link) — Cisco proprietarni, više se ne koristi
* **IEEE 802.1Q (dot1q)** — industrijski standard, koristi koncept VLAN taga

##### 2. Gdje se smješta VLAN tag?
VLAN tag se umeće u **Layer 2 Ethernet Frame**.
Točna pozicija: između polja Source MAC i EtherType.

**Standardni Ethernet Frame:**
```
[Preambla] [Odred. MAC] [Izvor. MAC] [Type] [Podaci] [FCS]
```

**802.1Q Tagged Frame:**
```
[Preambla] [Odred. MAC] [Izvor. MAC] [802.1Q Tag] [Type] [Podaci] [FCS]
```

---

## Verifikacija i rješavanje problema

| Naredba | Svrha |
| :--- | :--- |
| `show vlan brief` | Prikazuje sve konfigurirane VLAN-ove i dodijeljene access portove |
| `show interfaces trunk` | Prikazuje aktivne trunk portove, Native VLAN i listu dozvoljenih VLAN-ova |
| `show interface [id] switchport` | Detaljan izvještaj o administrativnom i operativnom stanju porta |

---

## Napomene za ispit
- VLAN-ovi su lokalni za switch — ne propagiraju se automatski (osim putem VTP-a)
- **Native VLAN** promet putuje trunk linkom **bez taga**
- **Access port** pripada točno jednom VLAN-u
- **Trunk port** prenosi promet više VLAN-ova
- VLAN 1 je default i Native VLAN — sigurnosna preporuka je promijeniti oba
- 802.1Q tag je dugačak **4 bajta** i umeće se u Ethernet frame

