# 06 - STP Protection

### Svrha STP zaštite
STP zaštita služi za stabilizaciju **Control Plane** logike unutar Layer 2 mreže, sprečavanje neovlaštenih promjena topologije i ubrzavanje rada na pristupnim portovima.

---

### 1. PortFast
Omogućava portu da iz **Blocking** stanja odmah pređe u **Forwarding** (preskače *Listening* i *Learning* faze). Koristi se isključivo za portove na kojima su krajnji uređaji (PC, Printer).

* **Globalno podešavanje:**
  `spanning-tree portfast default`
* **Interface podešavanje:**
  `spanning-tree portfast`

### 2. BPDU Guard
Sigurnosna značajka koja automatski gasi port (**err-disabled**) ako na PortFast portu primi BPDU. Sprječava spajanje neovlaštenih switcheva.

* **Globalno podešavanje:**
  `spanning-tree portfast bpduguard default`
* **Interface podešavanje:**
  `spanning-tree bpduguard enable`

### 3. BPDU Filter
Slično kao BPDU Guard, ali umjesto gašenja porta, on samo prestaje slati i procesirati BPDU frameove na tom sučelju.

* **Globalno podešavanje:**
  `spanning-tree portfast bpdufilter default`
* **Interface podešavanje:**
  `spanning-tree bpdufilter enable`

### 4. LoopGuard
Sprečava nastanak petlji u slučaju jednosmjernog kvara linka (kada port prestane primati BPDU, on ga ne prebacuje u Forwarding nego u **loop-inconsistent** stanje).

 * **Globalno podešavanje:**
  `Switch(config)# spanning-tree loopguard default`
  
 * **Interface podešavanje:**
  `Switch(config-if)# spanning-tree guard loop`

### 5. RootGuard
Sprečava da vanjski switch postane **Root Bridge**. Ako na portu stigne "bolji" (superior) BPDU, port se stavlja u **root-inconsistent** stanje dok god se taj BPDU šalje.

* **Interface podešavanje:**
  `spanning-tree guard root`

---

### Verifikacija i statusi
| Komanda | Svrha |
| :--- | :--- |
| `show spanning-tree summary` | Prikazuje koji su zaštitni mehanizmi aktivni globalno. |
| `show spanning-tree interface [id] detail` | Detaljan pregled zaštite na specifičnom portu. |
| `show spanning-tree inconsistentports` | Prikazuje portove koje su blokirali RootGuard ili LoopGuard. |
