# OUTSIDE-SW Configuration

## Role
The Outside Switch sits between pfSense LAN1 interface and CORE-SW.
It operates purely at Layer 2 with no VLAN configuration needed.

## Why Does This Switch Exist?
```
In this enterprise diagram, the Outside Switch:
- Provides a physical aggregation point
- Allows multiple devices to connect
  to the same network segment
- In production: connects pfSense cluster
  members to the same segment
- Mirrors real C9300 Outside Switch role
```

## Configuration
```
! Simple Layer 2 pass-through
! No VLANs needed - purely passing
! traffic between pfSense and CORE-SW

interface e0/0
 duplex full
 no shutdown

interface e0/1
 duplex full
 no shutdown

end
wr
```

## Key Point
```
pfSense e1 (172.16.10.1) and CORE-SW e0/0
(172.16.10.2) are both routed ports.

They communicate at Layer 3 directly.
OUTSIDE-SW just provides the physical
connection between them - no VLAN
tagging required!
```

---

# DMZ-SW Configuration

## Role
The DMZ Switch connects pfSense DMZ interface (e2) to DMZ servers.
Creates an isolated Layer 2 segment for the DMZ zone.

## Why a Dedicated DMZ Switch?
```
In enterprise networks, DMZ has its own switch because:
- Complete physical separation from LAN
- All DMZ traffic must pass through firewall
- Cannot bypass security controls
- Easy to add more DMZ servers
- Mirrors real NHS C9300 DMZ Switch role
```

## Configuration
```
! Simple Layer 2 switch
! pfSense e2 and DMZ-Server on same segment

interface e0/0
 duplex full
 no shutdown

interface e0/1
 duplex full
 no shutdown

end
wr
```

## Security Importance
```
DMZ-SW ensures:
- DMZ-Server gateway = pfSense (10.10.20.1)
- ALL DMZ traffic hits pfSense first
- Firewall rules enforced on ALL traffic
- DMZ cannot bypass firewall to reach LAN!

Without dedicated DMZ-SW (old design):
- DMZ connected to CORE-SW
- CORE-SW could route DMZ->LAN directly
- Firewall rules BYPASSED!
- Security compromise!
```
