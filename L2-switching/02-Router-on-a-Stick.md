# 02 - Inter-VLAN Routing: Router-on-a-Stick (RoaS)

**Concept:** Router-on-a-Stick is a method where a single physical interface on a router is used to route traffic between multiple VLANs on a network. This is achieved by creating logical **sub-interfaces** for each VLAN.

---



## Configuration Workflow

### 1. Switch-Side Configuration (Trunking)
*The port connected to the router must be configured as a trunk to carry multiple VLAN tags.*

```bash
 interface gigabitEthernet 0/1
 switchport trunk encapsulation dot1q  # If supported/required
 switchport mode trunk
```

### 2. Router-Side Configuration (Sub-interfaces)
We divide the physical interface into logical parts, one for each VLAN

```bash
 interface gigabitEthernet 0/0
 no shutdown                 # The physical interface must be UP

 interface gigabitEthernet 0/0.10
 encapsulation dot1q 10      # Binds this sub-interface to VLAN 10
 ip address 192.168.10.1 255.255.255.0

 interface gigabitEthernet 0/0.20
 encapsulation dot1q 20      # Binds this sub-interface to VLAN 20
 ip address 192.168.20.1 255.255.255.0
```

### 3.Verification and Troubleshooting
| Command | Purpose |
| :--- | :--- |
| `show ip interface brief` | Verifies that sub-interfaces are status "up" and protocol "up". |
| `show ip route` | Checks if the router sees the VLAN networks as "Directly Connected". |
| `show vlans` | (On Router) Displays a summary of sub-interface and VLAN ID mappings.. |
