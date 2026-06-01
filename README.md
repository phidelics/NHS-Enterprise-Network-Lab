# Enterprise Network Lab - pfSense & Cisco

## Overview
This lab replicates a real-world enterprise network infrastructure built in EVE-NG.
It demonstrates enterprise-grade network design including firewall zones, inter-VLAN routing, NAT, and DMZ architecture.

Inspired by enterprise healthcare network designs used in large organisations.

## Network Diagram
```
                    [WAN - Internet]
                    203.0.113.1/30
                          |
                     [pfSense FW]
                    /             \
              e1/LAN1           e2/LAN2
           172.16.10.1         10.10.20.1
                |                   |
          [OUTSIDE-SW]          [DMZ-SW]
                |                   |
          [CORE-SW]            [DMZ-Server]
          172.16.10.2          10.10.20.10
                |
          [ACCESS-SW]
                |
          [Staff-PC]
          10.10.10.10
```

## IP Address Scheme
| Device | Interface | IP Address | Subnet | Role |
|--------|-----------|------------|--------|------|
| pfSense | e0 (WAN) | 203.0.113.1 | /30 | WAN Interface |
| pfSense | e1 (LAN1) | 172.16.10.1 | /24 | LAN Interface |
| pfSense | e2 (LAN2) | 10.10.20.1 | /24 | DMZ Interface |
| CORE-SW | e0/0 | 172.16.10.2 | /24 | Uplink to pfSense |
| CORE-SW | Vlan10 | 10.10.10.1 | /24 | Staff Gateway |
| Staff-PC | ens3 | 10.10.10.10 | /24 | End User |
| DMZ-Server | ens3 | 10.10.20.10 | /24 | Web Server |

## Network Zones
| Zone | Subnet | Purpose |
|------|--------|---------|
| WAN | 203.0.113.0/30 | Simulated Internet |
| LAN/Staff | 10.10.10.0/24 | Internal Staff Network |
| DMZ | 10.10.20.0/24 | Server Zone |
| Transit | 172.16.10.0/24 | pfSense to Core link |

## Lab Components
| Device | Type | Role |
|--------|------|------|
| pfSense 2.6.0 | Firewall | Security/NAT/Routing |
| OUTSIDE-SW | Cisco IOU L2 | Outside switching layer |
| CORE-SW | Cisco IOU L3 | Core routing |
| ACCESS-SW | Cisco IOU L2 | Access layer |
| DMZ-SW | Cisco IOU L2 | DMZ switching |
| Staff-PC | Debian Linux | End user simulation |
| DMZ-Server | Debian Linux | Web server simulation |

## Key Concepts Demonstrated
- Zone-based firewall security
- Inter-VLAN routing
- NAT and Port Forwarding
- DMZ architecture
- Layer 2/3 switching hierarchy
- Enterprise traffic flow design
- Firewall rule ordering and stateful inspection

## Traffic Flow Rules
| Source | Destination | Action | Reason |
|--------|-------------|--------|--------|
| Staff | Internet | Allow | Staff need internet access |
| Staff | DMZ | Allow HTTP/HTTPS | Staff access web services |
| Staff | Firewall | Allow ICMP | Management/monitoring |
| DMZ | Staff | BLOCK | DMZ must never initiate to LAN |
| DMZ | Internet | BLOCK | Servers dont browse internet |
| Internet | DMZ | Allow port 8080 | NAT to web server |

## Enterprise Design Principles Applied
```
1. Default Deny - block everything, allow only what is needed
2. Zone Separation - WAN, DMZ, LAN completely isolated
3. Firewall as gateway - all inter-zone traffic inspected
4. DMZ for public services - servers never on internal LAN
5. NAT - hide internal infrastructure from users
6. Layered switching - Access, Core, Outside hierarchy
```

## Sections
1. [IP Planning](docs/IP-Plan.md)
2. [Switch Configurations](configs/)
3. [Firewall Rules](docs/Firewall-Rules.md)
4. [NAT Configuration](docs/NAT.md)

## Tools Used
- EVE-NG Community Edition
- pfSense 2.6.0
- Cisco IOU L2/L3 images
- Debian Linux

## Author
Built as part of CCNA studies and enterprise networking practice.
Demonstrates hands-on skills in network design, security, and troubleshooting.
