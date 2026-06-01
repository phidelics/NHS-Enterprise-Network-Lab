# ACCESS-SW Configuration

## Role
The Access Switch connects end user devices to the network.
It operates at Layer 2 only and segments users into VLANs.

## Why a Separate Access Switch?
```
In enterprise networks, access switches:
- Connect end user devices (PCs, phones, printers)
- Enforce VLAN membership per port
- Provide port security
- Are separate from core for scalability

NHS has one access switch per floor/department
Each with dual uplinks to both Core switches for HA
```

## Configuration
```
! Create VLAN
vlan 10
 name STAFF

! Trunk uplink to CORE-SW
! Carries all VLANs between switches
interface e0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 duplex full
 no shutdown

! Access port for Staff-PC
! Only carries VLAN 10 traffic
interface e0/1
 switchport mode access
 switchport access vlan 10
 duplex full
 no shutdown

end
wr
```

## Verification Commands
```
show vlan brief           ! Verify VLAN assignments
show interfaces trunk     ! Verify trunk to CORE-SW
show interfaces status    ! Verify port states
show mac address-table    ! Verify device MACs learned
```

## Key Concepts
```
Access Port:
- Connects end devices (PCs, phones)
- Carries traffic for ONE VLAN only
- Device doesn't need to know about VLANs
- Untagged traffic in/out

Trunk Port:
- Connects switches together
- Carries traffic for MULTIPLE VLANs
- Uses 802.1Q tags to identify VLANs
- Tagged traffic in/out
```
