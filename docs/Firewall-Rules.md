# Firewall Rules

## Philosophy
Enterprise firewall design follows one golden rule:
```
BLOCK EVERYTHING BY DEFAULT
Only allow exactly what is needed
From exactly who needs it
To exactly where it needs to go
On exactly the port required
```

## Zone Security Levels
| Zone | Trust Level | Description |
|------|-------------|-------------|
| WAN | Untrusted | Internet - assume hostile |
| DMZ | Semi-trusted | Servers accessible from internet |
| LAN/Staff | Trusted | Internal staff network |

## LAN1 (Staff Network) Rules
| Priority | Action | Protocol | Source | Destination | Port | Description |
|----------|--------|----------|--------|-------------|------|-------------|
| 1 | Allow | Any | Any | LAN1 Address | 443,80 | Anti-lockout (GUI access) |
| 2 | Allow | ICMP | 10.10.10.0/24 | This Firewall | Any | Staff can ping pfSense |
| 3 | Allow | TCP | 10.10.10.0/24 | 10.10.20.0/24 | 80,443 | Staff can reach DMZ servers |
| 4 | Allow | Any | 10.10.10.0/24 | Any | Any | Staff outbound internet |

## LAN2 (DMZ Network) Rules
| Priority | Action | Protocol | Source | Destination | Port | Description |
|----------|--------|----------|--------|-------------|------|-------------|
| 1 | BLOCK | Any | 10.10.20.0/24 | 10.10.10.0/24 | Any | DMZ cannot reach Staff |
| 2 | Allow | ICMP | 10.10.20.0/24 | This Firewall | Any | DMZ can ping pfSense |

## Why DMZ Cannot Reach Staff?
```
DMZ servers face the internet.
If a DMZ server gets compromised:
- Attacker controls the server
- If DMZ->LAN was allowed
- Attacker could reach patient data
- HR systems, clinical systems exposed!

This attack is called "Lateral Movement"
Blocking DMZ->LAN prevents this! 

This is a fundamental enterprise
security principle.
```

## Rule Order Matters!
```
pfSense reads rules TOP to BOTTOM.
First matching rule wins.

WRONG order:
1. Allow all from DMZ
2. Block DMZ to Staff
Result: Staff gets blocked because
        rule 1 matches first!

CORRECT order:
1. Block DMZ to Staff
2. Allow DMZ to ping firewall
Result: Staff protected, DMZ management works!
```

## Stateful Firewall
```
pfSense is a STATEFUL firewall.
It tracks connection state:

Client sends SYN -> pfSense allows it
Server sends SYN-ACK -> pfSense allows return
Connection established

Adding a block rule doesn't kill
existing connections immediately.
Must clear state table:
Diagnostics -> States -> Reset States
Or via CLI: pfctl -F states
```
