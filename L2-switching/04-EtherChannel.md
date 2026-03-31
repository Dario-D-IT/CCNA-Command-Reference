# 04 - EtherChannel
### Što je ETherchannel?
**ETherChannel**omogućava da više fizičkih interfacea grupiraš u **jedan logički interface**-i onda se ponašaju kao jedan interface.

**Layer 2 ETherchannel**  - grupa **switch portova** koji rade kao jedan interface

**Layer 3 Etherchannel** grupa **routed portova** koji rade kao jedan interface - kojem mozes dodjeliti IP adresu.

**STP** te fizičke interface tretirao kao jedan interface.

## Konfiguracija Layer 2 Etherchannela
### PAgP (Port Aggregation Protocol)
* Cisco proprietary protokol
* dinamički pregovara, kreira i održava ETHERCHANNEL

```bash
 channel-group 1 mode desirable     # aktivno pregovara
 channel-group 1 mode auto          # čeka na konekciju
```

### LACP   (Link Aggregation Control Protocol)
* Industry standard protocol (IEEE 802.3ad)
* dinamički pregovara, kreira i održava ETHERCHANNEL

```bash
 channel-group 1 mode active     # aktivno pregovara
 channel-group 1 mode pasive     # čeka na konekciju
```

### Static EtherChannel
* ne koristi se protokol za kreiranje etherchannel-a
* interfacei su statitčki konfigurirani

```bash
 channel-group 1 mode on     # aktivno pregovara
 channel-group 1 mode on     # aktivno pregovara
```
## Konfiguracija Layer 3 Etherchannela
```bash
 interface range G0/0-3
 no switchport
 channel-group 1 mode active
```
* uđem u novokreirani POrtChannel interface:
```bash
 interface Po1
 ip address 10.0.0.1 255.255.255.252

```

### Komande za verifikaciju:
```bash
 show etherchannel summary
 show interfaces port-channel 1 status
 show etherchannel port-channel
 show lacp neighbor
 show pagp neighbor
```


### Napomene:
* Channel-group broj : 1-256
* značajan je samo lokalno na uređaju, tj switch A moze imati channel-group=1, a switch B Moze imati channel-group= 10.

Da bi kreirali EtherChannel, ove postavke na fizičkim interfaceima moraju odgovarati:
* Duplex
* Speed
* Native VLAN i dozvoljeni VLAN-ovi
* Switchport mode - access ili trunk

Prilokom setiranja trunk-a na Etherchannel-u,
trunk se uvijek setira na POrtChannel portu , a ne na fizičkim portovima.

#### pojašnjenja pojmova:
* **EtherChannel** - ime za protoko, tehnologiju
* **Channel-group** - komanda za gruporanje fizičkih linkova u jedna logički
* **Port-Channel** - naziv virtualnog interfacea kojeg kreiramo sa sa Channel-group komandom
