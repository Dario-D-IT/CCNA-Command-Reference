# 02 - Inter-VLAN Routing: Router-on-a-Stick (RoaS)

## Svrha
Router-on-a-Stick je metoda gdje se **jedno fizičko sučelje na routeru** koristi za usmjeravanje prometa između više VLAN-ova. Postiže se kreiranjem logičkih **pod-sučelja (sub-interfaces)** za svaki VLAN.


---

## OSI sloj
Usmjeravanje se odvija na **Network Layeru (Layer 3)**, dok trunking radi na **Layer 2**.

---

## Konfiguracija

### 1. Konfiguracija switcha — Trunk port prema routeru
*Port koji je spojen na router mora biti konfiguriran kao trunk kako bi prenosio tagove više VLAN-ova.*

```bash
interface gigabitEthernet 0/1
switchport trunk encapsulation dot1q
switchport mode trunk
```

### 2. Konfiguracija routera — Pod-sučelja (Sub-interfaces)
Fizičko sučelje dijelimo na logičke dijelove — po jedno pod-sučelje za svaki VLAN.

```bash
interface gigabitEthernet 0/0
no shutdown                      # Fizičko sučelje mora biti UP
no ip address                    # Ukloni IP adresu s fizičkog porta

interface gigabitEthernet 0/0.10
encapsulation dot1q 10           # Veže pod-sučelje za VLAN 10
ip address 192.168.10.1 255.255.255.0

interface gigabitEthernet 0/0.20
encapsulation dot1q 20           # Veže pod-sučelje za VLAN 20
ip address 192.168.20.1 255.255.255.0
```

> **Napomena:** Broj pod-sučelja (npr. `.10`) ne mora odgovarati broju VLAN-a, ali je to uobičajena praksa radi preglednosti.

---

## Verifikacija

| Naredba | Svrha |
| :--- | :--- |
| `show ip interface brief` | Provjera da su pod-sučelja u stanju "up/up" |
| `show ip route` | Provjera vidi li router VLAN mreže kao "Directly Connected" |
| `show vlans` | (Na routeru) Prikaz svih pod-sučelja i njihovih VLAN preslikavanja |
| `show interfaces gigabitEthernet 0/0.10` | Detalji specifičnog pod-sučelja |

---

## Napomene za ispit
- Fizičko sučelje mora biti **UP** ali **bez IP adrese**
- `encapsulation dot1q [vlan-id]` je **obavezna** naredba na svakom pod-sučelju
- Za **Native VLAN** dodaj `native` na kraju: `encapsulation dot1q 1 native`
- Router-on-a-Stick ima ograničenje propusnosti jer sav Inter-VLAN promet prolazi kroz jedan fizički link
- Alternativa: **Layer 3 switch** s SVI sučeljima (bolji za veće mreže)
