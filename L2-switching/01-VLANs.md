01 - VLANs & Trunking (802.1Q)

**Concept:** VLANs (Virtual Local Area Networks) allow  logical segmentation of a physical switch into multiple broadcast domains, improving security, performance, and manageability.

A BROADCAST DOMAIN is the group of devices which will receive a BROADCAST FRAME (Destination MAC : FFFF.FFFF.FFFF) sent by any one of the members.

# 01 - VLANs & Trunking (802.1Q)

**Concept:** VLANs (Virtual Local Area Networks) allow for logical segmentation of a physical switch into multiple broadcast domains, improving security, performance, and manageability.

---

## Configuration: Access & Trunk Ports

### 1. Creating and Naming a VLAN
```bash
vlan 10
name SALES_DEPT
```

### 2. Assigning an Access Port
```bash
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 10
```
### 3.Trunk link configuration
```bash
interface gigabitEthernet 0/1
 switchport trunk encapsulation dot1q  # Obavezno na nekim 3560/3750 modelima
 switchport mode trunk
```



