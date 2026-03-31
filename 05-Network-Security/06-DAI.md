# 06 - DAI — Dynamic ARP Inspection

## Svrha
DAI štiti mrežu od **ARP Spoofing / ARP Poisoning napada**. Napadač šalje lažne ARP odgovore i uvjerava ostale uređaje da je njegova MAC adresa vezana za nečiju IP adresu — čime presreće promet (**Man-in-the-Middle**).

DAI provjerava svaki ARP paket na untrusted portovima u odnosu na **DHCP Snooping Binding tablicu**. Ako MAC-IP kombinacija ne odgovara — paket se odbacuje.


---

## OSI sloj
Radi na **Data Link Layeru (Layer 2)** — provjerava ARP pakete.

---

## Preduvjet
> ⚠️ **DHCP Snooping mora biti uključen** — DAI koristi njegovu Binding tablicu za provjeru.
> Za statički konfigurirane uređaje (koji ne koriste DHCP) potrebno je dodati ručne ARP ACL unose.

---

## Konfiguracija

```bash
# Preduvjet: DHCP Snooping mora biti aktivan
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10

# Korak 1: Uključi DAI za specifični VLAN
Switch(config)# ip arp inspection vlan 10
Switch(config)# ip arp inspection vlan 10,20,30       # Više VLAN-ova

# Korak 2: Postavi trusted port (isti trusted portovi kao kod DHCP Snooping)
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip arp inspection trust

# Korak 3: (Opcionalno) Ograniči brzinu ARP paketa na untrusted portu
# Sprječava ARP flooding napad
Switch(config)# interface FastEthernet0/1
Switch(config-if)# ip arp inspection limit rate 100   # max 100 ARP paketa/sekundi

# Za statičke uređaje bez DHCP (npr. serveri s fiksnom IP) — ručni ARP ACL
Switch(config)# arp access-list STATICNI_UREDAJI
Switch(config-arp-acl)# permit ip host 192.168.10.100 mac host AA.BBCC.DD11.2233
Switch(config)# ip arp inspection filter STATICNI_UREDAJI vlan 10
```

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ip arp inspection` | Globalni DAI status i konfigurirani VLAN-ovi |
| `show ip arp inspection vlan 10` | DAI status za specifični VLAN |
| `show ip arp inspection interfaces` | Trusted/Untrusted status po sučeljima |
| `show ip arp inspection statistics` | Broj prosljeđenih i odbijenih ARP paketa |

**Primjer outputa:**
```
Switch# show ip arp inspection statistics
Vlan  Forwarded  Dropped  DHCP Drops  ACL Drops
----  ---------  -------  ----------  ---------
  10       1250        3           3          0
```

---

## Usporedba DHCP Snooping i DAI

| Karakteristika | DHCP Snooping | DAI |
| :--- | :--- | :--- |
| Štiti od | Rogue DHCP servera | ARP Spoofing napada |
| Provjerava | DHCP pakete | ARP pakete |
| Gradi tablicu | Da (Binding tablica) | Ne (koristi Snooping tablicu) |
| Trusted portovi | Da | Da (isti logika) |
| Preduvjet | — | DHCP Snooping aktivan |

---

## Napomene za ispit
- DAI **ovisi o DHCP Snooping Binding tablici** — mora biti uključen zajedno
- **Svi portovi su Untrusted po defaultu** — postavi trusted samo prema uplinku/switchu
- Za statičke uređaje (bez DHCP): koristi **ARP ACL** da definiraš dozvoljene MAC-IP parove
- DAI štiti od **ARP Spoofing** i **ARP Poisoning** napada
- Trusted portovi za DAI i DHCP Snooping obično su **isti portovi** (uplink prema distribucijskom switchu)
