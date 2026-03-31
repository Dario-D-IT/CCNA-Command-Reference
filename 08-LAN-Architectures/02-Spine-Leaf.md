# 02 - Spine-Leaf arhitektura

## Svrha
Spine-Leaf je arhitektura dizajnirana za **podatkovne centre (Data Centers)** gdje je ključan brz i predvidiv promet između servera. Optimizirana je za **East-West** promet.


---

## 1. Komponente

```
        ┌──────────┐         ┌──────────┐
        │  SPINE 1 │─────────│  SPINE 2 │   ← Spine: L3 switchevi visokih performansi
        └────┬─────┘         └────┬─────┘
        ╱    │    ╲           ╱   │    ╲
       ╱     │     ╲         ╱    │     ╲
 ┌────┴─┐ ┌──┴──┐ ┌─┴────┐ ┌─────┴┐ ┌──┴──┐
 │LEAF 1│ │LEAF2│ │LEAF 3│ │LEAF 4│ │LEAF5│  ← Leaf: L2/L3 switchevi
 └──────┘ └─────┘ └──────┘ └──────┘ └─────┘
    |          |        |        |       |
 Serveri    Serveri  Serveri  Serveri Serveri
```

### Spine switchevi
- **Backbone** podatkovnog centra
- Samo prosljeđuju promet između Leaf switcheva
- **Ne spajaju se međusobno direktno**
- Visoke performanse, niska latencija

### Leaf switchevi
- Direktno spajaju **servere, storage, ostalu opremu**
- **Svaki Leaf** je spojen na **svaki Spine**
- Rub mreže — ovdje se provode sigurnosne politike

---

## 2. East-West vs North-South promet

| Smjer | Opis | Primjer |
| :--- | :--- | :--- |
| **East-West** | Promet između servera **unutar** podatkovnog centra | Server A komunicira s bazom podataka |
| **North-South** | Promet koji **ulazi/izlazi** iz podatkovnog centra | Korisnik s Interneta pristupa web serveru |

> U modernim data centrima **80%+ prometa** je East-West (mikroservisi, virtualizacija, kontejneri) — zato je Spine-Leaf optimalniji od Three-Tier modela.

---

## 3. Usporedba: Three-Tier vs Spine-Leaf

| Karakteristika | Three-Tier | Spine-Leaf |
| :--- | :--- | :--- |
| Dizajniran za | Campus mreže | Podatkovne centre |
| East-West promet | ne bas optimalan | Optimalan |
| Latencija | Varijabilna | **Predvidiva i konstantna** |
| Skalabilnost | Ograničena | Visoka (dodaj Leaf/Spine) |
| Broj hopova | Varijabilan | **Uvijek 2** (Leaf→Spine→Leaf) |
| Redundancija | STP (blokira portove) | ECMP (svi putevi aktivni) |

---

## 4. Prednosti Spine-Leaf arhitekture

- **Predvidiva latencija** — promet između bilo koja dva servera uvijek prolazi kroz točno **2 hopa** (Leaf → Spine → Leaf)
- **ECMP** (Equal-Cost Multi-Path) — svi linkovi prema Spine switchevima su aktivni istovremeno (nema blokiranih portova kao kod STP-a)
- **Laka skalabilnost** — dodaj novi Leaf switch i spoji ga na sve Spine switcheve
- **Nema single point of failure** — svaki Leaf ima put do svakog Spinea

---

## 5. Ograničenja Spine-Leaf arhitekture

- **Leaf switchevi se ne spajaju direktno** — sav promet mora ići kroz Spine
- **Spine switchevi se ne spajaju međusobno** — dodavanje veza između Spine-ova narušava arhitekturu
- Skuplje od klasičnog Three-Tier modela za manje instalacije
- Dizajnirano za **Data Center**, nije optimalno za Campus mreže

---

## Napomene za ispit
- Spine-Leaf je dizajniran za **podatkovne centre**, Three-Tier za **campus mreže**
- Promet između dva servera uvijek prolazi kroz **točno 2 hopa**
- **Svaki Leaf spojen je na svaki Spine** — nikad direktno Leaf-na-Leaf
- **Spine switchevi se ne spajaju međusobno**
- **ECMP** omogućuje da svi linkovi budu aktivni (za razliku od STP koji blokira)
- **East-West** = server-to-server promet unutar DC-a (Spine-Leaf optimiziran za ovo)
- **North-South** = promet prema/iz vanjskog svijeta
