# 03 - Alati za automatizaciju mreže

## Svrha
Alati za automatizaciju eliminiraju ručnu konfiguraciju mrežnih uređaja — umjesto SSH sesija i copy-paste naredbi, definiraš **željeno stanje** infrastrukture i alat ga automatski primjenjuje.

---

## 1. Ansible

### Što je Ansible?
Ansible je **agentless** alat za automatizaciju — ne treba instalirati nikakav softver na mrežne uređaje. Komunicira putem **SSH** (ili HTTPS za API-based uređaje).


### Ključni pojmovi

| Pojam | Opis |
| :--- | :--- |
| **Playbook** | YAML datoteka s popisom zadataka (što napraviti) |
| **Inventory** | Popis uređaja na kojima se izvršavaju zadaci |
| **Module** | Gotova funkcija za specifičan zadatak (npr. `ios_config`) |
| **Task** | Jedan korak unutar Playbooka |
| **Role** | Skup Playbooka organiziranih po funkciji |
| **Idempotent** | Pokretanje istog Playbooka više puta ne uzrokuje probleme |

### Primjer Ansible Playbooka za Cisco IOS
```yaml
---
- name: Konfiguracija VLAN-ova na switchevima
  hosts: switches
  gather_facts: no

  tasks:
    - name: Kreiraj VLAN 10
      cisco.ios.ios_vlans:
        config:
          - vlan_id: 10
            name: PRODAJA
        state: merged

    - name: Postavi hostname
      cisco.ios.ios_config:
        lines:
          - hostname SW1

    - name: Spremi konfiguraciju
      cisco.ios.ios_command:
        commands:
          - write memory
```

### Primjer Inventory datoteke
```ini
[switches]
sw1 ansible_host=192.168.1.10
sw2 ansible_host=192.168.1.11

[routers]
r1 ansible_host=192.168.1.1

[all:vars]
ansible_user=admin
ansible_password=Lozinka123
ansible_network_os=ios
ansible_connection=network_cli
```

### Prednosti Ansible-a
- **Agentless** — nema instalacije na uređajima
- **YAML** — čitljiv i razumljiv format
- **Veliki ekosustav modula** za Cisco IOS, NX-OS, ASA, DNA Center...

---

## 2. Terraform

### Što je Terraform?
Terraform je alat za **Infrastructure as Code (IaC)** — definiraš infrastrukturu u deklarativnim konfiguracijskim datotekama (HCL jezik), a Terraform je kreira i upravlja njome.

> Za razliku od Ansible-a koji je fokusiran na **konfiguraciju**, Terraform je fokusiran na **kreiranje i upravljanje infrastrukturom** (cloud resursi, mreže, VM-ovi).

### Ključni pojmovi

| Pojam | Opis |
| :--- | :--- |
| **Provider** | Plugin koji komunicira s određenom platformom (AWS, Azure, Cisco) |
| **Resource** | Infrastrukturni objekt koji Terraform upravlja (VM, mreža, VLAN) |
| **State** | Terraform prati trenutno stanje infrastrukture u `.tfstate` datoteci |
| **Plan** | Prikazuje što će Terraform promijeniti prije primjene |
| **Apply** | Primjenjuje promjene na infrastrukturu |
| **Idempotent** | Pokretanje `apply` više puta ne duplicira resurse |

### Terraform workflow
```
1. terraform init    → Preuzmi potrebne providere
2. terraform plan    → Prikaži što će se promijeniti
3. terraform apply   → Primijeni promjene
4. terraform destroy → Ukloni infrastrukturu
```

### Primjer Terraform konfiguracije (Cisco ACI)
```hcl
terraform {
  required_providers {
    aci = {
      source = "CiscoDevNet/aci"
    }
  }
}

provider "aci" {
  username = "admin"
  password = "Lozinka123"
  url      = "https://apic.tvrtka.hr"
}

resource "aci_tenant" "production" {
  name        = "Produkcija"
  description = "Produkcijski tenant"
}

resource "aci_vrf" "main_vrf" {
  tenant_dn = aci_tenant.production.id
  name      = "Main-VRF"
}
```

---

## 3. Usporedba Ansible vs Terraform

| Karakteristika | Ansible | Terraform |
| :--- | :--- | :--- |
| Primarna svrha | Konfiguracija uređaja | Kreiranje infrastrukture |
| Pristup | Proceduralni (kako) | Deklarativan (što) |
| Format | YAML | HCL (HashiCorp Config Language) |
| Agent | Agentless (SSH) | Agentless (API) |
| State management | Ne prati state | Prati state (.tfstate) |
| Mrežna primjena | Cisco IOS/NX-OS konfiguracija | Cloud mreže, SDN (ACI) |
| Idempotentnost | Da (moduli) | Da (nativno) |

### Kada koristiti koji?
```
Ansible  → Konfiguracija Cisco routera/switcheva, deploy softvera
Terraform → Kreiranje cloud mreža (AWS VPC, Azure VNet), SDN infrastruktura
```

---

## 4. Usporedba klasičnog vs automatiziranog upravljanja

| Karakteristika | Ručno (CLI) | Automatizacija |
| :--- | :--- | :--- |
| Brzina | Spora | Brza (tisuće uređaja odjednom) |
| Greške | Česte (human error) | Minimalne |
| Konzistentnost | Varijabilna | Garantirana |
| Dokumentacija | Ručna | Konfiguracija = dokumentacija |
| Skalabilnost | Loša | Odlična |
| Revizija promjena | Teška | Git version control |

---

## Napomene za ispit
- **Ansible** = agentless, YAML playbooks, SSH komunikacija, konfiguracija uređaja
- **Terraform** = Infrastructure as Code, HCL format, state file, cloud i SDN
- **Idempotentnost** = pokretanje više puta daje isti rezultat — bez duplikata
- Ansible komunicira s Cisco uređajima putem **SSH** ili **HTTPS API**
- Terraform prati stanje infrastrukture u **.tfstate** datoteci
- Ansible je bolji za **konfiguraciju**, Terraform za **provisioning infrastrukture**
- `terraform plan` = prikaži promjene; `terraform apply` = primijeni ih
