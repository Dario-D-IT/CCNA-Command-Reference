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

### 1. Creating and Naming a VLAN



