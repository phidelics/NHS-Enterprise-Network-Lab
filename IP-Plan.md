# IP Address Planning

## Design Principles
In enterprise networking, IP planning is done BEFORE any configuration begins.
Every subnet serves a specific purpose and is sized appropriately.

## Subnets
| Network | Subnet | Gateway | Purpose |
|---------|--------|---------|---------|
| WAN | 203.0.113.0/30 | ISP | Simulated public internet link |
| Transit | 172.16.10.0/24 | 172.16.10.1 | pfSense LAN1 to CORE-SW link |
| Staff/LAN | 10.10.10.0/24 | 10.10.10.1 | Internal staff computers |
| DMZ | 10.10.20.0/24 | 10.10.20.1 | DMZ servers |

## Why /30 for WAN?
```
203.0.113.0/30 gives only 2 usable IPs:
203.0.113.1 = pfSense WAN
203.0.113.2 = ISP router

Point-to-point links only need 2 IPs
Using /24 would waste 253 addresses!
This is efficient IP management.
```

## Why RFC 5737 for WAN?
```
203.0.113.0/24 is reserved for documentation
and lab use (RFC 5737 TEST-NET-3).
Using this avoids conflicts with real
internet IP addresses.
```

## Why Separate Transit Network?
```
172.16.10.0/24 is used between pfSense
and CORE-SW. This is called a transit
or point-to-point network.

Benefits:
- Clear separation of zones
- Easy to identify traffic in logs
- pfSense can apply rules per zone
```

## Device IP Assignments
| Device | Interface | IP | Subnet Mask | Default Gateway |
|--------|-----------|-----|-------------|-----------------|
| pfSense | WAN (e0) | 203.0.113.1 | /30 | ISP |
| pfSense | LAN1 (e1) | 172.16.10.1 | /24 | - |
| pfSense | LAN2 (e2) | 10.10.20.1 | /24 | - |
| CORE-SW | e0/0 | 172.16.10.2 | /24 | 172.16.10.1 |
| CORE-SW | Vlan10 | 10.10.10.1 | /24 | - |
| Staff-PC | ens3 | 10.10.10.10 | /24 | 10.10.10.1 |
| DMZ-Server | ens3 | 10.10.20.10 | /24 | 10.10.20.1 |
