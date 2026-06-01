# NAT Configuration

## Types of NAT

### Source NAT (SNAT/Masquerade)
```
Changes the SOURCE IP of outbound packets.

Example:
Staff-PC (10.10.10.10) browses internet
pfSense changes source to 203.0.113.1
Internet sees pfSense WAN IP only!

Used for: Internal users going outbound
Benefits: Hides internal IP addresses
          Conserves public IP addresses
```

### Destination NAT (DNAT/Port Forward)
```
Changes the DESTINATION IP of inbound packets.

Example:
User requests http://172.16.10.1:8080
pfSense translates to 10.10.20.10:80
DMZ web server receives the request!

Used for: External/internal users reaching
          internal servers
Benefits: Server IP is hidden
          Multiple servers behind one IP
          Security through obscurity
```

## Port Forward Configuration

### Staff to DMZ Web Server
| Setting | Value |
|---------|-------|
| Interface | LAN1 |
| Protocol | TCP |
| Destination | LAN1 Address |
| Destination Port | 8080 |
| Redirect Target | 10.10.20.10 |
| Redirect Port | 80 |
| Description | Staff to DMZ Web Server |

## How It Works
```
Staff-PC types: http://172.16.10.1:8080

1. Packet arrives at pfSense LAN1
2. pfSense checks NAT rules
3. Matches port forward rule
4. Destination changed:
   172.16.10.1:8080 -> 10.10.20.10:80
5. Packet forwarded to DMZ-Server
6. DMZ-Server responds to pfSense
7. pfSense translates back
8. Staff-PC receives response

Staff-PC never knows real server IP!
```

## Security Benefits
```
Without NAT:
Staff-PC knows DMZ-Server IP (10.10.20.10)
Could attempt direct connections
Could bypass firewall rules!

With NAT:
Staff-PC only knows pfSense IP
DMZ-Server completely hidden
All traffic inspected by firewall
```

## Real World Application
```
Enterprise public website example:
User types: https://www.homeoffice.uk
DNS resolves to public IP
Firewall NAT translates to internal server
User never knows internal infrastructure!

This protects against:
- Direct server attacks
- IP scanning/enumeration  
- Unauthorised access attempts
```
