# 02 - REST API i formati podataka

## Svrha
REST API i standardizirani formati podataka omogućuju **automatizirano upravljanje mrežnom opremom** — umjesto ručnog konfiguriranja putem CLI-ja, šaljemo strukturirane zahtjeve koje uređaji automatski obrađuju.

---

## 1. REST API osnove

**REST** (Representational State Transfer) je arhitekturalni stil za komunikaciju između sustava putem **HTTP/HTTPS protokola**.

### HTTP metode

| Metoda | CRUD operacija | Opis | Primjer |
| :--- | :--- | :--- | :--- |
| **GET** | Read | Dohvati podatke | Čitaj konfiguraciju sučelja |
| **POST** | Create | Kreiraj novi resurs | Dodaj novi VLAN |
| **PUT** | Update (zamjena) | Zamijeni cijeli resurs | Zamijeni cijelu konfiguraciju |
| **PATCH** | Update (djelomično) | Izmijeni dio resursa | Promijeni samo IP adresu |
| **DELETE** | Delete | Obriši resurs | Ukloni VLAN |

### HTTP statusni kodovi

| Kod | Značenje |
| :---: | :--- |
| **200** | OK — zahtjev uspješan |
| **201** | Created — resurs kreiran |
| **204** | No Content — uspješno, nema sadržaja za vratiti |
| **400** | Bad Request — greška u zahtjevu |
| **401** | Unauthorized — neispravna autentifikacija |
| **403** | Forbidden — nedovoljne privilegije |
| **404** | Not Found — resurs ne postoji |
| **500** | Internal Server Error — greška na serveru |

### REST API primjer (Cisco DNA Center)
```
GET https://sandboxdnac.cisco.com/dna/intent/api/v1/network-device
Headers:
  Content-Type: application/json
  X-Auth-Token: eyJhbGc...

Odgovor (200 OK):
{
  "response": [
    {
      "hostname": "switch1.tvrtka.hr",
      "managementIpAddress": "10.0.0.1",
      "platformId": "C9300-48P"
    }
  ]
}
```

---

## 2. JSON — JavaScript Object Notation

Najčešći format za razmjenu podataka s REST API-jem. Čitljiv za ljude i strojeve.

### Struktura
```json
{
  "uredaj": {
    "hostname": "R1",
    "interfaces": [
      {
        "name": "GigabitEthernet0/0",
        "ip": "192.168.1.1",
        "mask": "255.255.255.0",
        "status": "up"
      },
      {
        "name": "GigabitEthernet0/1",
        "ip": "10.0.0.1",
        "mask": "255.255.255.252",
        "status": "up"
      }
    ],
    "vlans": [10, 20, 30]
  }
}
```

### JSON tipovi podataka
| Tip | Primjer |
| :--- | :--- |
| String | `"hostname": "R1"` |
| Number | `"vlan": 10` |
| Boolean | `"enabled": true` |
| Array | `"vlans": [10, 20, 30]` |
| Object | `"interface": { "name": "Gi0/0" }` |
| Null | `"description": null` |

---

## 3. XML — Extensible Markup Language

Stariji format, još uvijek korišten u NETCONF protokolu.

```xml
<uredaj>
  <hostname>R1</hostname>
  <interface>
    <name>GigabitEthernet0/0</name>
    <ip>192.168.1.1</ip>
    <mask>255.255.255.0</mask>
  </interface>
</uredaj>
```

---

## 4. YAML — YAML Ain't Markup Language

Koristi se u **Ansible playbook** datotekama. Čitljiviji od JSON-a za ljude.

```yaml
uredaj:
  hostname: R1
  interfaces:
    - name: GigabitEthernet0/0
      ip: 192.168.1.1
      mask: 255.255.255.0
      status: up
    - name: GigabitEthernet0/1
      ip: 10.0.0.1
      mask: 255.255.255.252
      status: up
  vlans:
    - 10
    - 20
    - 30
```

---

## 5. NETCONF i RESTCONF

| Protokol | Transport | Format | Svrha |
| :--- | :--- | :--- | :--- |
| **NETCONF** | SSH (TCP 830) | XML | Konfiguracija i upravljanje mrežnim uređajima |
| **RESTCONF** | HTTPS (TCP 443) | JSON ili XML | REST API pristup YANG modelima |

### YANG — Yet Another Next Generation
**YANG** je jezik za modeliranje podataka koji definira **strukturu konfiguracije** mrežnih uređaja. NETCONF i RESTCONF koriste YANG modele.

```
YANG model → definira strukturu podataka
NETCONF/RESTCONF → prenosi podatke prema/od uređaja
```

---

## Napomene za ispit
- **GET** = čitaj, **POST** = kreiraj, **PUT** = zamijeni, **DELETE** = briši
- **200** = OK, **201** = Created, **404** = Not Found, **401** = Unauthorized
- **JSON** = najčešći REST API format (vitičaste zagrade `{}`, uglate `[]`)
- **YAML** = format za Ansible (uvlačenje umjesto zagrada)
- **XML** = format za NETCONF (tagovi `<tag>`)
- **NETCONF** koristi SSH port **830**, **RESTCONF** koristi HTTPS port **443**
- **YANG** = model podataka koji definira strukturu konfiguracije
