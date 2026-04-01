# 01 - WAN tehnologije i topologije

## Svrha
WAN (Wide Area Network) povezuje **geografski udaljene lokacije** — poslovnice, podatkovne centre i Internet. Za razliku od LAN-a kojim upravljamo sami, WAN infrastrukturu najčešće pruža **ISP (Internet Service Provider)**.

---

## OSI sloj
WAN tehnologije pokrivaju **Physical (Layer 1)** i **Data Link (Layer 2)**, a usmjeravanje je na **Layer 3**.

---

## 1. WAN terminologija

| Pojam | Opis |
| :--- | :--- |
| **CPE** (Customer Premises Equipment) | Oprema na strani korisnika (router, modem) |
| **CO** (Central Office) | Telekomunikacijski čvor ISP-a |
| **Local Loop** | Veza između CPE i CO (tzv. "zadnja milja") |
| **Demarcation Point** | Točka predaje odgovornosti između korisnika i ISP-a |
| **CSU/DSU** | Uređaj koji konvertira digitalni signal za WAN prijenos |

---

## 2. WAN tehnologije

### Dedicated Leased Lines (iznajmljeni vodovi)
- **Privatna, dedicirane veza** između dvije lokacije
- Konstantna propusnost, visoka pouzdanost
- Skupo — plaća se neovisno o korištenju
- Standardi: **T1** (1.544 Mbps), **T3** (44.7 Mbps), **E1** (2.048 Mbps)

### MPLS (Multiprotocol Label Switching)
- ISP gradi **privatnu mrežu** koja izgleda kao jedan veliki switch
- Promet se usmjerava na temelju **labela** (ne IP adresa) — brže
- Korisnik dobiva **privatni VRF** (Virtual Routing and Forwarding)
- Podržava **QoS** — kritičan promet (VoIP) dobiva prioritet
- Skuplje od Interneta, ali pouzdanije i sigurnije

```
Lokacija A ─── MPLS mreža ISP-a ─── Lokacija B
                     │
               Lokacija C
```

### Metro Ethernet
- Ethernet tehnologija proširena na **gradsko (Metropolitan) područje**
- Korisnik dobiva **Ethernet port** kao da je direktno spojen na LAN
- Visoke brzine, relativno povoljno
- Tipovi: **E-Line** (point-to-point), **E-LAN** (multipoint), **E-tree** (hub and spoke)

### Internet Broadband tehnologije

| Tehnologija | Medij | Brzina | Napomena |
| :--- | :--- | :--- | :--- |
| **DSL** (Digital Subscriber Line) | Telefonska linija (bakar) | Do 100 Mbps | Download > Upload (ADSL) |
| **Cable** | Koaksijalni kabel (TV) | Do 1 Gbps | Dijeli se s korisnicima |
| **Fiber (FTTH)** | Optički kabel | Do 10 Gbps | Najbrži, simetričan |
| **4G/5G Wireless** | Radio valovi | Do 1 Gbps (5G) | Mobilna veza, backup link |
| **Satellite** | Satelit | Do 100 Mbps | Visoka latencija |

---

## 3. WAN topologije

### Point-to-Point
```
Lokacija A ────────── Lokacija B
```
- Direktna veza između **dvije točke**
- Jednostavno, predvidivo, skupo pri skaliranju

### Hub-and-Spoke (zvjezdasta)
```
        Lokacija B
             │
Lokacija C ──┼── HQ (Hub)
             │
        Lokacija D
```
- **Centralni hub** (HQ) spojen sa svim **spoke** lokacijama
- Jeftiniji od Full Mesh — manje veza
- **Nedostatak:** Spoke-to-Spoke promet mora ići preko Hub-a (suboptimalno)

### Full Mesh
```
A ─── B
│╲   ╱│
│  ╳  │
│╱   ╲│
C ─── D
```
- Svaka lokacija spojena na **svaku drugu**
- Maksimalna redundancija i performanse
- **Skupo** — broj veza: `n(n-1)/2`

### Partial Mesh
- Kompromis između Hub-and-Spoke i Full Mesh
- Kritične lokacije imaju više veza, ostale manje

---

## Napomene za ispit
- **T1** = 1.544 Mbps, **E1** = 2.048 Mbps (europski standard)
- **MPLS** usmjerava prema **labelama**, ne IP adresama — brže od klasičnog routinga
- **DSL** dijeli bakrenu telefonsku liniju — brzina ovisi o udaljenosti od CO
- **Hub-and-Spoke**: malo veza, ali sve ide kroz hub — suboptimalno za Spoke-Spoke promet
- **Full Mesh**: `n(n-1)/2` veza — 4 lokacije = 6 veza, 10 lokacija = 45 veza
- **Demarcation Point** = granica između odgovornosti korisnika i ISP-a

