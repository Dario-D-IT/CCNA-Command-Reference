# 05 - Spanning-Tree Protocol

STP sprječava petlje na Layer 2 nivou (Switching loops) tako što blokira redundantne veze. Stvara topologiju bez petlji s jednim centralnim switchem koji se zove **Root Bridge**.

### OSI Layer: 
* Radi na Data Link Layer (Layer 2).
#### Mogući Spanning-Tree protokoli na Cisco switchevima:
| STP Protocol | standard |show spanning-tree|
| :--- | :--- | :--- |
| `PVTS+` | 802.1d|ieee|
| `rapid-pvts+` | 802.1w |rstp|

### 1. Kako STP radi (The Election Process)

STP koristi SPA algoritam da odluči koji će port biti blokiran , a koji ne.To radi po slijedećoj logici:
* **Izbor RootBridgea**
   * Switch s najnižim Bridge ID-om postaje glavni
   * Bridge ID = Priority + MAC adresa
   * defaultni priority = 32768
   * inkrement = 4096
   * ako su proirity jednaki, gleda se MAC adresa
   * niže je bolje u oba slučaja

* **Izbor Root portova** - non Root switchevi biraju port koji će ih voditi do RootSwitcha
   * cijena koštanja do Root switcha
   * najniži Bridge ID od upstream switcha
   * najniži Port priority od upstream switcha
   * najniži broj porta od upstream switcha

* **Izbor Designated i non designated portova**
   * najniža cijena koštanja do RootBridgea
   * najniži Bridge ID od lokalnog switcha
   * najniži fizički port broj od lokalnog switcha

* **Bridge Priority vrijednosti**
  * 0-65535
  * 32 768 - defaultna vrijednost
  * 28 672 - root secondary
  * 24 576 - root primary (ispravljen broj zbog inkrementa)
  * 4096 - inkrement

* **Port Priority vrijednost**
  * 0-240
  * 128 - defaultna vrijednost
  * 16 - inkrement
  
  
### 2. Tablica cijene koštanja 
| brzina porta | cijena |
| :--- | :--- |
| `10GB` | 2 |
| `1GB` | 4 |
| `100MB` | 19 |
| `10MB`  |100 |

### 3. Stanja portova (Port States) 

Da li switch šalje i prima na nekom portu:
* redovan promet
* BPDU poruke
* MAC adrese (uči li ili ne)
#### Moguća stanja portova
* **Blocking**: Ne šalje podatke, samo sluša BPDU poruke
* **LIstening**:
* **Learning**: uči MAC adrese, ali još ne šalje podatke
* **Forwarding**:normalan rad

### 4. Uloge portova (Port roles)

Kako STP vidi mreznu topologiju:
* **Root Port**
* **Designated Port**
* **Blocking Port**

### 5. BPD-u
#### Što je BPD-u:
BPDU je okvir (frame) koji switchevi razmjenjuju kako bi uspostavili i održavali topologiju bez petlji. To je Control Plane poruka koja se šalje na specifičnu multicast MAC adresu (01:00:0c:cc:cc:cd - za cisco STP).
##### Bitne karakteristike:
 * **Učestalost**: switchevi šalju BPDU svake 2 seknude (Hello timer)
 * **Sadržaj**: unutra se nalazi u Bridge ID

##### Što BPDU sadrži:
* Root Switch ID
* Senders Switch ID
* Senders root cost
* Timersi:
  *Hello: 2 sec
  Max age: 20 sec
  Forward delay timers: 15+15 sec

##### napomene o BPDU:
* kod STP-a - svi ga šalju u početku
* nakon izbora RootBridgea - šalje ga samo RootBridge
* drugi ga samo forwardaju na svoje designated portove
* designated portovi - šalju/proslijeđuju  BPDU
* root i non designated portovi - primaju BPDU
### 6. Spanning Tree Timers
| STP Timer | svrha |trajanje|
| :--- | :--- | :--- |
| `Hello` | koliko često Root Bridhe šalje Hello BPDU |2sec|
| `Forward delay` | koliko dugo switch ostaje u Listening and Learning stanju( 15 sec svaki) |15sec|
| `Max Age` | nakon koliko vremena interface mijenja topologiju, nakon što prestane primati Hello BPDU |2Osec (10xHello)|

### 7. Konfiguracija
#### Promjena verzije spanning-tree protokola:
* Switch(config)#spanning-tree mode pvst  
* Switch(config)#spanning-tree mode rapid-pvst

#### Postavljanje prioriteta da switch postane Root Bridge
Switch(config)# spanning-tree vlan 10 priority 4096
* defaultni priority: 32768
* inkrement: 4096

#### Postavljanje prioriteta na portu (interface)
Switch(config)# spanning-tree vlan 10 port-priority <0-240>
* defaultn port prioroty: 128
* inkrement : 16

#### Alternativne (lakše) komande za Root Bridge i secondary Root Bridge
Switch(config)# spanning-tree vlan 10 root primary (root primary priority: 24 567)

Switch(config)# spanning-tree vlan 20 root secondary (root secondary priority: 28 672)

#### Ubrzavanje rada za portove na kojima su PC-ovi (PortFast)
Switch(config-if)# spanning-tree portfast
#### Dodatna zaštita uz PortFast (isključuje port ako primi BPDU)
Switch(config-if)# spanning-tree bpduguard enable


### 7. Verifikacija
* show spannin-tree
* show spanning-tree vlan [id]
* show spanning-tree summary

