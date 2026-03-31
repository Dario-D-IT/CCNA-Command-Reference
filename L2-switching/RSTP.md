# 07 - Rapid Spanning Tree Protocol (RSTP - 802.1w)

### Svrha protokola
RSTP je evolucija STP-a koja omogućuje brzu konvergenciju mrežne topologije (ispod 6 sekundi). Radi na **Data Link Layer (Layer 2)**.

---

### 1. Sličnosti s 802.1D (STP)
Osnovna logika **Control Plane** procesa ostaje ista:
* **Root Bridge** se bira na isti način (najniži Bridge ID).
* **Root Port** se bira na isti način (najniži Path Cost do Root-a).
* **Designated Port** se bira na isti način.

### 2. Razlike u cijeni (Cost)
RSTP koristi 32-bitne vrijednosti za Cost (802.1w standard) kako bi podržao brzine veće od 1 Gbps.

| Brzina sučelja | RSTP Cost (802.1w) |
| :--- | :--- |
| 10 Mbps | 2,000,000 |
| 100 Mbps | 200,000 |
| **1 Gbps** | **20,000** |
| 10 Gbps | 2,000 |

### 3. Port Roles (Uloge portova)
RSTP dijeli staru "Non-Designated" rolu u dvije specifične uloge:
* **Alternate:** Rezervni put do Root Bridgea (**Backup za Root Port**).
* **Backup:** Rezervni put do istog segmenta (**Backup za Designated Port**). Javlja se samo ako je switch spojen na Hub.

### 4. Hello poruke i Neighbor Loss
* **Hello poruke:** U STP-u ih generira samo Root, a u RSTP-u **svaki switch** generira i šalje svoje Hello poruke svakih 2s.
* **Neighbor loss:** RSTP reagira puno brže. Čeka samo **3x Hello (ukupno 6 sekundi)** prije nego proglasi prekid veze, dok STP čeka 20s.

### 5. RSTP Link Types
* **Edge:** Port na koji je spojen End host. Odmah ide u *Forwarding* (isto što i PortFast).
* **Point-to-point:** Direktna veza između dva switcha (Full-Duplex mod).
* **Shared:** Veza na Hub (radi isključivo u **Half-Duplex** modu).

### 6. Konfiguracija 
```bash
# Aktiviranje RSTP načina rada
Switch(config)# spanning-tree mode rapid-pvst

# Postavljanje prioriteta (višekratnici broja 4096)
Switch(config)# spanning-tree vlan 10 priority 24576

# podešavanje tipa linka između dva switcha
Switch(config-if)# spanning-tree link-type point-to-point

# podešavanje tipa linka između switcha i hub-a
Switch(config-if)# spanning-tree link-type shared
```
### 7. Važne napomene o izračunu (Cost)
Root Bridge šalje BPDU s cijenom nula.

Kalkulacija: Novi Cost = primljeni Cost + Cost ulaznog porta (Incoming Port Cost).

Smjer: Informacije o topologiji teku od Root Bridgea prema ostatku mreže.

> **Usporedba s OSPF-om:** Kod OSPF-a izračun cijene puta ide iz **suprotnog smjera** — od izvora (source) prema odredištu (destination). OSPF broji cijenu **izlaznog porta** (Outgoing/Egress interface), dok STP/RSTP broji cijenu **ulaznog porta** (Incoming interface) na putu prema Root Bridgeu.


