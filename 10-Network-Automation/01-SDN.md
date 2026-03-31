# 01 - SDN i kontroleri

## Svrha
SDN (Software Defined Networking) odvaja **upravljačku logiku (Control Plane)** od **prosljeđivanja prometa (Data Plane)** u mrežnim uređajima. Centralizirani kontroler preuzima donošenje odluka umjesto svakog pojedinog uređaja.


---

## 1. Tradicionalna vs SDN arhitektura

### Tradicionalna mreža
```
┌─────────────────┐    ┌─────────────────┐
│    Router A     │    │    Router B     │
│ ┌─────────────┐ │    │ ┌─────────────┐ │
│ │Control Plane│ │    │ │Control Plane│ │  ← Svaki uređaj sam odlučuje
│ └─────────────┘ │    │ └─────────────┘ │
│ ┌─────────────┐ │    │ ┌─────────────┐ │
│ │  Data Plane │ │    │ │  Data Plane │ │  ← Svaki uređaj sam prosljeđuje
│ └─────────────┘ │    │ └─────────────┘ │
└─────────────────┘    └─────────────────┘
```

### SDN arhitektura
```
         ┌──────────────────────────┐
         │     SDN KONTROLER        │  ← Centralizirana inteligencija
         │   (Control Plane)        │
         └──────────┬───────────────┘
         Northbound API │  (prema aplikacijama)
              ┌─────┴─────┐
    ┌─────────┴──┐     ┌──┴─────────┐
    │  Switch A  │     │  Switch B  │  ← Samo prosljeđuju (Data Plane)
    └────────────┘     └────────────┘
```

---

## 2. Tri razine SDN arhitekture

| Razina | Naziv | Opis |
| :--- | :--- | :--- |
| **Gornja** | Application Layer | Poslovne aplikacije, mrežne aplikacije |
| **Srednja** | Control Layer | SDN kontroler — centralizirana logika |
| **Donja** | Infrastructure Layer | Fizički mrežni uređaji (switchevi, routeri) |

### API sučelja
- **Northbound API** — između kontrolera i aplikacija (REST API, Python)
- **Southbound API** — između kontrolera i mrežnih uređaja (OpenFlow, NETCONF, RESTCONF)

---

## 3. Razdvajanje ravnina (Planes)

| Ravnina | Naziv | Funkcija | Primjer |
| :--- | :--- | :--- | :--- |
| **Management Plane** | Upravljačka | Konfiguracija i monitoring uređaja | SSH, SNMP, NETCONF |
| **Control Plane** | Upravljačka prometom | Donosi odluke o usmjeravanju | OSPF, STP, ARP |
| **Data Plane** | Podatkovna | Prosljeđuje pakete prema tablici | IP forwarding, Ethernet switching |

---

## 4. Cisco DNA Center — IBN platforma

**DNA Center** (Digital Network Architecture Center) je Ciscova **Intent-Based Networking (IBN)** platforma za automatizaciju kampus mreža.

### Što je IBN?
Umjesto da konfiguriraš svaki uređaj posebno, definiraš **namjeru** (intent):
```
"Hoću da VLAN 10 ima pristup Internetu, ali ne i VLAN 20"
```
DNA Center automatski prevodi tu namjeru u konfiguraciju na svim uređajima.

### DNA Center komponente

| Komponenta | Uloga |
| :--- | :--- |
| **Design** | Definiranje topologije, IP plana, mrežnih profila |
| **Policy** | Sigurnosne i QoS politike |
| **Provision** | Automatska konfiguracija uređaja |
| **Assurance** | Monitoring, analitika i troubleshooting (AI/ML) |

### DNA Center vs tradicionalni Cisco Prime

| Karakteristika | Cisco Prime | DNA Center |
| :--- | :--- | :--- |
| Pristup | Uređaj po uređaj | Centraliziran, policy-based |
| Automatizacija | Ograničena | Potpuna (ZTP, template) |
| Analitika | Osnovna | AI/ML bazirani uvidi |
| API | Ograničen | Potpun REST API |

---

## 5. Cisco SD-Access

SD-Access je Ciscovo rješenje za **automatizaciju kampus mreža** temeljeno na DNA Centeru:

- **Fabric** — overlay mreža koja enkapsulira promet (VXLAN)
- **Segmentacija** bez kompleksnih ACL-ova
- **Makro i mikro segmentacija** — VN (Virtual Network) i SGT (Scalable Group Tag)

---

## Napomene za ispit
- **SDN** = odvaja Control Plane od Data Plane
- **Northbound API** = kontroler ↔ aplikacije; **Southbound API** = kontroler ↔ uređaji
- **OpenFlow** je najpoznatiji Southbound API protokol
- **DNA Center** = Ciscova IBN platforma za kampus mreže
- **IBN** = definiraš namjeru, sustav sam konfigurira uređaje
- **Management Plane** ≠ Control Plane — Management je za konfiguraciju uređaja (SSH/SNMP), Control je za routing logiku (OSPF/STP)
- DNA Center koristi **REST API** za integraciju s vanjskim sustavima
