# FHRP — First Hop Redundancy Protocols

## Svrha
FHRP protokoli osiguravaju **redundanciju defaultnog gatewaya** za end-uređaje. Ako primarni router padne, backup router preuzima bez ikakve promjene konfiguracije na klijentima.

> **Problem koji rješava:** Laptop ima konfiguriran default gateway `192.168.1.1`. Ako taj router padne, laptop više nema pristup internetu. FHRP stvara **virtualnu IP adresu** koju dijele dva ili više routera — klijent uvijek koristi virtualnu IP, a koji fizički router je aktivan — transparentno se mijenja.

---

## OSI sloj
Radi na **Network Layeru (Layer 3)**.

---

## Usporedba FHRP protokola

| Protokol | Standard | Razvio | Broj aktivnih | Autentifikacija |
| :--- | :--- | :--- | :--- | :--- |
| **HSRP v1** | Cisco proprietary | Cisco | 1 aktivan, 1 standby | MD5 |
| **HSRP v2** | Cisco proprietary | Cisco | 1 aktivan, 1 standby | MD5 |
| **VRRP** | IEEE 802.1AB | Open standard | 1 master, više backup | MD5 |
| **GLBP** | Cisco proprietary | Cisco | Više aktivnih (load balancing) | MD5 |

---

## HSRP — Hot Standby Router Protocol

### HSRP stanja uređaja
```
Initial → Learn → Listen → Speak → Standby → Active
```
- **Active** = trenutno prosljeđuje promet
- **Standby** = spreman preuzeti ako Active padne
- **Listen** = zna za grupu, ali nije ni Active ni Standby

### Konfiguracija HSRP
```bash
# Na PRIMARY routeru (viši prioritet = postaje Active)
Router1(config)# interface GigabitEthernet0/0
Router1(config-if)# ip address 192.168.1.2 255.255.255.0
Router1(config-if)# standby 1 ip 192.168.1.1        # Virtualna IP (group 1)
Router1(config-if)# standby 1 priority 110           # Default je 100, viši = Active
Router1(config-if)# standby 1 preempt                # Preuzima natrag ako se vrati online

# Na BACKUP routeru
Router2(config)# interface GigabitEthernet0/0
Router2(config-if)# ip address 192.168.1.3 255.255.255.0
Router2(config-if)# standby 1 ip 192.168.1.1        # Ista virtualna IP
Router2(config-if)# standby 1 priority 90            # Niži prioritet = Standby

# HSRP verzija 2 (podržava IPv6, više grupa)
Router1(config-if)# standby version 2
```

### HSRP tracking — preempt na temelju stanja sučelja
```bash
# Ako Gi0/1 padne, smanji prioritet za 20 (110-20=90 → postaje Standby)
Router1(config-if)# standby 1 track GigabitEthernet0/1 decrement 20
```

---

## VRRP — Virtual Router Redundancy Protocol

```bash
# Konfiguracija je slična HSRP-u, ali koristi "vrrp" umjesto "standby"
Router1(config-if)# vrrp 1 ip 192.168.1.1
Router1(config-if)# vrrp 1 priority 110
Router1(config-if)# vrrp 1 preempt
```

---

## Verifikacija

| Naredba | Što prikazuje |
| :--- | :--- |
| `show standby` | Detaljan HSRP status (Active/Standby, virtualna IP, prioritet) |
| `show standby brief` | Kratak pregled HSRP grupa |
| `show vrrp` | VRRP status |
| `show vrrp brief` | Kratak VRRP pregled |
| `show glbp` | GLBP status |

**Primjer ispravnog HSRP outputa:**
```
Router# show standby brief
P indicates configured to preempt.
Interface   Grp  Pri P State   Active addr    Standby addr   Group addr
Gi0/0       1    110 P Active  local          192.168.1.3    192.168.1.1
```

---

## Napomene za ispit
- **Virtualna IP** = default gateway za klijente (ne mijenja se nikad)
- **Viši prioritet = Active router** (default priority: 100)
- **Preempt** = router automatski preuzima Active ulogu kad se vrati s višim prioritetom
- HSRP koristi multicast `224.0.0.2` (v1) ili `224.0.0.102` (v2)
- VRRP koristi multicast `224.0.0.18`
- **GLBP** jedini podržava **load balancing** između više aktivnih routera
- HSRP portovi: **UDP 1985** (v1), **UDP 1985** (v2)
