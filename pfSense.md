# pfSense Firewall Configuration

## Role
pfSense is the security brain of the network.
It controls all traffic between zones and provides NAT services.

## Interface Assignment
| Interface | EVE-NG Port | IP Address | Zone |
|-----------|-------------|------------|------|
| INTERNET (WAN) | vtnet0/e0 | 203.0.113.1/30 | WAN |
| LAN1 (LAN) | vtnet1/e1 | 172.16.10.1/24 | Transit/LAN |
| LAN2 (OPT1) | vtnet2/e2 | 10.10.20.1/24 | DMZ |

## Static Routes
```
Network:  10.10.10.0/24
Gateway:  172.16.10.2 (CORE-SW)
Reason:   Staff network is behind CORE-SW
          pfSense cannot reach it directly
          Must be told where it is!

Note: 10.10.20.0/24 does NOT need a
static route because pfSense e2 is
directly connected to that subnet!
```

## Why Static Route for Staff but not DMZ?
```
Directly connected = pfSense knows automatically
Behind another device = must add static route

pfSense e2 = 10.10.20.1/24 (directly connected)
pfSense doesn't need to be told about 10.10.20.0/24

Staff network (10.10.10.0/24) is behind CORE-SW
pfSense has no direct connection to it
Must add: route to 10.10.10.0/24 via 172.16.10.2
```

## Firewall Rules Summary

### LAN1 Rules (Staff Network)
```
1. Anti-lockout rule (system - cannot remove)
2. Allow ICMP from Staff to This Firewall
3. Allow TCP from Staff to DMZ (port 80,443)
4. Allow any from Staff to anywhere (internet)
```

### LAN2 Rules (DMZ Network)
```
1. BLOCK any from DMZ to Staff network
2. Allow ICMP from DMZ to This Firewall
```

## NAT Port Forward
```
Interface:       LAN1
Protocol:        TCP
Destination:     LAN1 Address (172.16.10.1)
Destination Port: 8080
Redirect to:     10.10.20.10
Redirect Port:   80
Purpose:         Staff access DMZ web server
                 via pfSense IP
```

## Verification
```
! From pfSense shell (option 8):

! Check routing table
netstat -rn

! Check firewall rules loaded
pfctl -sr

! Check specific rules
pfctl -sr | grep 10.10.10

! Clear state table after rule changes
pfctl -F states

! Temporarily disable firewall for testing
pfctl -d

! Re-enable firewall
pfctl -e

! Test connectivity
ping -c 3 172.16.10.2    ! CORE-SW
ping -c 3 10.10.20.10    ! DMZ-Server
ping -c 3 10.10.10.10    ! Staff-PC
```

## Key Security Concepts

### Anti-Lockout Rule
```
pfSense automatically creates this rule
to prevent you locking yourself out of GUI.
Allows access to GUI from LAN interface.
Cannot be removed.
```

### Default Deny
```
pfSense blocks ALL traffic by default.
You must explicitly allow what you need.
This is correct enterprise security posture.
```

### Stateful Inspection
```
pfSense tracks connection state.
Outbound connections automatically
allow return traffic.
Block rules don't kill existing sessions.
Must clear state table: pfctl -F states
```
