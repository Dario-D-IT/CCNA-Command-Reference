# 01 - Hijerarhijska mrežna arhitektura

## Svrha
Hijerarhijski model dijeli mrežu u logičke slojeve s jasno definiranim ulogama. Cilj je **skalabilnost, upravljanje i redundancija** — svaki sloj ima specifičnu funkciju.


---

## 1. Three-Tier model (troslojna arhitektura)

```
         ┌─────────────────────────────┐
         │         CORE LAYER          │  ← Brzi backbone, spaja Distribution
         └──────────────┬──────────────┘
              ┌─────────┴─────────┐
    ┌─────────┴──────┐   ┌────────┴─────────┐
    │  DISTRIBUTION  │   │   DISTRIBUTION   │  ← Policy, usmjeravanje između blokova
    └────────┬───────┘   └────────┬─────────┘
       ┌─────┴─────┐         ┌───┴─────┐
    ┌──┴──┐     ┌──┴──┐   ┌──┴──┐  ┌──┴──┐
    │ ACC │     │ ACC │   │ ACC │  │ ACC │  ← Krajnji korisnici
    └─────┘     └─────┘   └─────┘  └─────┘
```

---

## 2. Uloge slojeva

### Access Layer (Pristupni sloj)
- Direktno spaja **krajnje uređaje** (PC, telefoni, pisači, AP-ovi)
- Implementira **Port Security, VLAN dodjelu, PoE**
- Uređaji: **Layer 2 switchevi**
- Nema usmjeravanja — prosljeđuje promet prema Distribution sloju

### Distribution Layer (Distribucijski sloj)
- **Agregira** promet s Access sloja
- Implementira **ACL, QoS, routing između VLAN-ova**
- Granica između Layer 2 (Access) i Layer 3 (Core)
- Uređaji: **Layer 3 switchevi / routeri**
- Obično dolazi u **paru** radi redundancije

### Core Layer (Jezgreni sloj)
- **Brzi backbone** koji spaja Distribution blokove
- **Ne filtrira, ne provodi politike** — samo brzo prosljeđuje
- Uređaji: **Visokokapacitetni Layer 3 switchevi**
- Redundancija je kritična — kvar = gubitak cijele mreže

---

## 3. Two-Tier / Collapsed Core (dvoslojna arhitektura)

U manjim mrežama Core i Distribution se **spajaju u jedan sloj**:

```
    ┌──────────────────────────────────┐
    │    DISTRIBUTION + CORE (Collapsed)│  ← Oba sloja na istim uređajima
    └───────────┬──────────────────────┘
         ┌──────┴──────┐
      ┌──┴──┐       ┌──┴──┐
      │ ACC │       │ ACC │                ← Krajnji korisnici
      └─────┘       └─────┘
```

**Kada koristiti:**
- Manje firme ili kampusi s jednom zgradom
- Manji troškovi (manje uređaja)
- Lakše upravljanje

---

## 4. Usporedba modela

| Karakteristika | Three-Tier | Two-Tier |
| :--- | :--- | :--- |
| Skalabilnost | Visoka | Srednja |
| Troškovi | Viši | Niži |
| Kompleksnost | Viša | Niža |
| Primjena | Veliki kampusi, Enterprise | Manje mreže |
| Redundancija | Potpuna | Djelomična |

---

## 5. Važni dizajnerski principi

### Redundancija linkova
```
Access switch ─── uplink 1 ──→ Distribution switch 1
             └─── uplink 2 ──→ Distribution switch 2
```
Ako jedan Distribution switch padne — Access switch ima alternativni put.

### Layer 2 vs Layer 3 boundary
- **Layer 2 domena** završava na Distribution sloju
- **Layer 3 usmjeravanje** počinje na Distribution sloju
- STP radi unutar Layer 2 domene — manji STP domen = brža konvergencija

---

## Napomene za ispit
- **Access** = krajnji uređaji, L2 switch, Port Security, PoE, VLAN dodjela
- **Distribution** = agregacija, L3 switch, ACL, routing između VLAN-ova
- **Core** = brzi backbone, **nema filtriranja**, samo prosljeđivanje
- **Collapsed Core** = Distribution + Core na istim uređajima (manje mreže)
- Distribution uvijek dolazi u **paru** radi redundancije
- Što veća mreža → Three-Tier; što manja → Two-Tier
