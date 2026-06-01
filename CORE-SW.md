# CORE-SW Configuration

## Role
The Core Switch is the Layer 3 routing hub for the internal network.
It routes traffic between VLANs and forwards all unknown traffic to pfSense.

## Why Layer 3 Switch?
```
In enterprise networks, routing between
VLANs at the core is done by a Layer 3
switch rather than a router because:

- Wire-speed routing (much faster)
- Single device for switching + routing
- Lower latency than router-on-a-stick
- Scalable for multiple VLANs
```

## Configuration
```
! Enable Layer 3 routing
ip routing

! Create VLANs
vlan 10
 name STAFF

! VLAN 10 SVI - Staff gateway
interface vlan 10
 ip address 10.10.10.1 255.255.255.0
 no shutdown

! Routed uplink to pfSense
! "no switchport" converts to Layer 3 port
interface e0/0
 no switchport
 ip address 172.16.10.2 255.255.255.0
 duplex full
 no shutdown

! Trunk to ACCESS-SW
interface e0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 duplex full
 no shutdown

! Default route to pfSense
! All unknown traffic goes to firewall
ip route 0.0.0.0 0.0.0.0 172.16.10.1

end
wr
```

## Verification Commands
```
show ip route          ! Verify routing table
show ip interface brief ! Verify interface IPs
show vlan brief        ! Verify VLANs
show interfaces trunk  ! Verify trunk ports
show arp               ! Verify ARP table
ping 172.16.10.1       ! Test pfSense reachability
```

## Expected Routing Table
```
S* 0.0.0.0/0 via 172.16.10.1      ! Default to pfSense
C  10.10.10.0/24 Vlan10            ! Staff network
C  172.16.10.0/24 Ethernet0/0      ! Transit to pfSense
```

## Key Concepts
```
"no switchport" - converts port from Layer 2
to Layer 3 so it can have an IP address.

SVI (Switch Virtual Interface) - virtual
interface for each VLAN that acts as the
default gateway for devices in that VLAN.

Default route - sends ALL unknown traffic
to pfSense which then makes routing/security
decisions. This is correct enterprise design.
```
