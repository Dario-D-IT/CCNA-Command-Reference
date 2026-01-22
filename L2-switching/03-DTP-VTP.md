# 03 - DTP & VTP Configuration Reference

**Focus:** Automating trunk negotiation (DTP) and VLAN database synchronization (VTP).

---

## DTP (Dynamic Trunking Protocol)
DTP is used to negotiate trunk links between Cisco switches. 

### Disable DTP (Security Best Practice)
To prevent unauthorized trunking and VLAN hopping, disable negotiation on user-facing ports.
```bash
  interface fastEthernet 0/1
  switchport mode access
  switchport nonegotiate  # Disables DTP frames entirely
```
## VTP (VLAN Trunking Protocol)
VTP synchronizes the vlan.dat file across a VTP domain

**Core Configuration Commands**
| Command | Purpose |
|:--- | :--- |
|`vtp domain ALGEBRA_LAB` |Sets the VTP domain name |
|`vtp password CISCO` |Optional: Sets a password for synchronization|
|`vtp mode server` |Sets the VTP mode (server/client/transparent/off) |
|`vtp version 2` |Sets the VTP version (standard is 2) |


**Verification**
| Command | Purpose |
|:--- | :--- |
|`show vtp status` |Displays domain, version, mode, and revision number |
|`show vtp password` |Displays the configured VTP password|

**Quick Check Table**
| Command | Purpose |
|:--- | :--- |
|`show vtp status` | Check VTP Revision|
|`switchport mode trunk + switchport nonegotiate` |Force Trunk (No DTP)|
|`show vtp counters` | View VTP Counters |

