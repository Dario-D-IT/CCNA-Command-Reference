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
