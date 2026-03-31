# 03 - Zaglavlja mrežnih protokola (Headers)

## Svrha
Razumijevanje strukture L2, L3 i L4 zaglavlja ključno je za dijagnostiku mreže — svaki sloj dodaje vlastite informacije koje određuju kako se podaci isporučuju od izvora do odredišta.

---

## 1. Layer 2 — Ethernet Frame (802.3)

```
 ┌──────────┬──────────┬──────┬──────────────────────┬─────┬──────────┐
 │ Preamble │  SFD     │ Dst  │   Src MAC            │Type │  Data    │  FCS  │
 │  7 B     │  1 B     │ MAC  │   6 B                │ 2 B │ 46-1500B │  4 B  │
 │          │          │  6 B │                      │     │          │       │
 └──────────┴──────────┴──────┴──────────────────────┴─────┴──────────┴───────┘
```

### Polja Ethernet framea

| Polje | Veličina | Opis |
| :--- | :---: | :--- |
| **Preamble** | 7 B | Sinkronizacija — izmjenični 10101010... uzorак |
| **SFD** (Start Frame Delimiter) | 1 B | Signalizira početak framea (10101011) |
| **Destination MAC** | 6 B | MAC adresa primatelja |
| **Source MAC** | 6 B | MAC adresa pošiljatelja |
| **EtherType / Length** | 2 B | Protokol višeg sloja: `0x0800`=IPv4, `0x0806`=ARP, `0x86DD`=IPv6 |
| **Data (Payload)** | 46–1500 B | Podaci višeg sloja (IP paket + padding ako < 46 B) |
| **FCS** (Frame Check Sequence) | 4 B | CRC provjera integriteta |

### MAC adresa struktura
```
  OUI (Organizationally Unique Identifier)  |  NIC specifično
  48:2C:6A                                  |  E2:B7:F1
  (3 bajta — dodjeljuje IEEE)                  (3 bajta — dodjeljuje proizvođač)
```

### Posebne MAC adrese
| MAC adresa | Tip | Namjena |
| :--- | :--- | :--- |
| `FF:FF:FF:FF:FF:FF` | Broadcast | Šalje se svim uređajima u LAN-u |
| `01:00:5E:xx:xx:xx` | Multicast | IPv4 multicast (IANA rezervirano) |
| `33:33:xx:xx:xx:xx` | Multicast | IPv6 multicast |

---

## 2. Layer 3 — IPv4 Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |Version|  IHL  |     DSCP    |ECN|         Total Length        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |         Identification        |Flags|      Fragment Offset    |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |  Time to Live |    Protocol   |         Header Checksum       |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                       Source Address                          |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                    Destination Address                        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                    Options (if IHL > 5)                       |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### Polja IPv4 headera

| Polje | Veličina | Opis |
| :--- | :---: | :--- |
| **Version** | 4 bita | IP verzija: `4` za IPv4 |
| **IHL** (Internet Header Length) | 4 bita | Duljina headera u 32-bitnim riječima (min. 5 = 20 B) |
| **DSCP** (Differentiated Services) | 6 bita | QoS — prioritizacija prometa |
| **ECN** (Explicit Congestion Notification) | 2 bita | Signalizacija zagušenja mreže |
| **Total Length** | 16 bita | Ukupna veličina paketa (header + data), max 65.535 B |
| **Identification** | 16 bita | ID za fragmentaciju — isti ID = dijelovi istog paketa |
| **Flags** | 3 bita | `DF` (Don't Fragment), `MF` (More Fragments) |
| **Fragment Offset** | 13 bita | Pozicija fragmenta u originalnom paketu |
| **TTL** (Time to Live) | 8 bita | Broj hopova — svaki router smanjuje za 1; pri 0 paket se odbacuje |
| **Protocol** | 8 bita | Protokol višeg sloja: `6`=TCP, `17`=UDP, `1`=ICMP, `89`=OSPF |
| **Header Checksum** | 16 bita | Provjera ispravnosti samo headera (ne podataka) |
| **Source IP** | 32 bita | IP adresa pošiljatelja |
| **Destination IP** | 32 bita | IP adresa primatelja |
| **Options** | Varijabilno | Opcionalno — rijetko se koristi |

### Ključne vrijednosti Protocol polja
| Broj | Protokol |
| :---: | :--- |
| 1 | ICMP (ping, traceroute) |
| 6 | TCP |
| 17 | UDP |
| 47 | GRE (tuneli) |
| 89 | OSPF |

---

## 3. Layer 4 — TCP zaglavlje

TCP (Transmission Control Protocol) = **connection-oriented**, pouzdana isporuka, flow control.

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |          Source Port          |       Destination Port        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                        Sequence Number                        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                    Acknowledgment Number                      |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |  Data |           |U|A|P|R|S|F|                               |
 | Offset| Reserved  |R|C|S|S|Y|I|            Window             |
 |       |           |G|K|H|T|N|N|                               |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |           Checksum            |         Urgent Pointer        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### Polja TCP zaglavlja

| Polje | Veličina | Opis |
| :--- | :---: | :--- |
| **Source Port** | 16 bita | Izvorišni port (1-65535) |
| **Destination Port** | 16 bita | Odredišni port (1-65535) |
| **Sequence Number** | 32 bita | Redni broj bajta — omogućuje redoslijed isporuke |
| **Acknowledgment Number** | 32 bita | Sljedeći očekivani bajt od druge strane |
| **Data Offset** | 4 bita | Duljina TCP headera u 32-bitnim riječima (min. 5 = 20 B) |
| **Flags (Control bits)** | 6 bita | SYN, ACK, FIN, RST, PSH, URG |
| **Window** | 16 bita | Flow control — koliko bajta može primiti bez potvrde |
| **Checksum** | 16 bita | Provjera integriteta headera i podataka |
| **Urgent Pointer** | 16 bita | Pointer na hitne podatke (koristi se s URG flagom) |

### TCP 3-way handshake
```
Klijent              Server
  │──── SYN ──────────►│   SEQ=100
  │◄─── SYN-ACK ───────│   SEQ=300, ACK=101
  │──── ACK ──────────►│   ACK=301
  │                    │
  │   (veza uspostavljena)
  │
  │──── FIN ──────────►│   (zatvaranje veze)
  │◄─── ACK ───────────│
  │◄─── FIN ───────────│
  │──── ACK ──────────►│
```

### Dobro poznati TCP portovi
| Port | Protokol |
| :---: | :--- |
| 21 | FTP (control) |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |

---

## 4. Layer 4 — UDP zaglavlje

UDP (User Datagram Protocol) = **connectionless**, brz, bez garancije isporuke.

```
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |          Source Port          |       Destination Port        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |             Length            |            Checksum           |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### Polja UDP zaglavlja

| Polje | Veličina | Opis |
| :--- | :---: | :--- |
| **Source Port** | 16 bita | Izvorišni port |
| **Destination Port** | 16 bita | Odredišni port |
| **Length** | 16 bita | Ukupna duljina UDP headera + podataka (min. 8 B) |
| **Checksum** | 16 bita | Opcionalna provjera integriteta |

### Dobro poznati UDP portovi
| Port | Protokol |
| :---: | :--- |
| 53 | DNS |
| 67/68 | DHCP (server/client) |
| 69 | TFTP |
| 123 | NTP |
| 161/162 | SNMP (agent/trap) |
| 514 | Syslog |

---

## 5. TCP vs UDP usporedba

| Karakteristika | TCP | UDP |
| :--- | :---: | :---: |
| Uspostava veze | 3-way handshake | Nema |
| Pouzdanost | Da (ACK, retransmisija) | Ne |
| Redoslijed paketa | Garantiran | Nije garantiran |
| Flow control | Da (Window size) | Ne |
| Overhead | Veći (20 B header) | Manji (8 B header) |
| Brzina | Sporiji | Brži |
| Primjena | HTTP, HTTPS, FTP, SSH | DNS, DHCP, VoIP, video |

---

## 6. Enkapsulacija — putovanje podataka kroz slojeve

```
Aplikacijski sloj:  [ DATA ]
Transport (L4):     [ TCP/UDP header | DATA ]           → Segment
Mrežni (L3):        [ IP header | TCP header | DATA ]   → Paket
Podatkovni (L2):    [ ETH header | IP | TCP | DATA | FCS ] → Frame
Fizički (L1):       Bitovi (0 i 1) → električni signal, svjetlost, radio val
```

> **De-enkapsulacija** = suprotan proces — svaki sloj skida svoje zaglavlje i prosljeđuje višem sloju.

---

## Napomene za ispit 
- Ethernet frame: min. **64 B**, max. **1518 B** (bez tagginga), **1522 B** s 802.1Q tagom
- **FCS** = Frame Check Sequence — ako se ne slaže, frame se odbacuje (ne retransmitira — to je zadatak TCP-a)
- **TTL** — tipično 64 (Linux) ili 128 (Windows); svaki router smanjuje za 1
- **Protocol 6** = TCP, **Protocol 17** = UDP, **Protocol 1** = ICMP — zapamti!
- TCP = **3-way handshake** (SYN → SYN-ACK → ACK), UDP = bez handshakea
- **Window size** = flow control; **Sequence/ACK numbers** = pouzdana isporuka
- UDP header = samo **8 bajtova** (source port, dst port, length, checksum)
- DHCP koristi UDP **67** (server) i **68** (client) — zašto UDP? Klijent nema IP za TCP
- DNS koristi UDP **53** za upite, TCP **53** za zone transfere
