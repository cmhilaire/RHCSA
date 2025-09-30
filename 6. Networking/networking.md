Networking (nmcli, ip, ss) – RHEL 9

Table of Contents
- [Introduction](#introduction)
- [NetworkManager with nmcli](#networkmanager-with-nmcli)
  - [Key Concepts](#key-concepts)
  - [Essential Commands](#essential-commands)
    - [View Network Status](#view-network-status)
    - [Manage Ethernet Connections](#manage-ethernet-connections)
    - [Real-time Monitoring](#real-time-monitoring)
- [IP Command Suite](#ip-command-suite)
  - [Key Concepts](#key-concepts-1)
  - [Essential Commands](#essential-commands-1)
    - [Interface Management (ip link)](#interface-management-ip-link)
    - [IP Address Management (ip addr)](#ip-address-management-ip-addr)
    - [Routing Management (ip route)](#routing-management-ip-route)
    - [Neighbor Management (ip neigh)](#neighbor-management-ip-neigh)
- [Socket Statistics with ss](#socket-statistics-with-ss)
  - [Key Concepts](#key-concepts-2)
  - [Essential Commands](#essential-commands-2)
    - [Basic Socket Information](#basic-socket-information)
    - [Advanced Filtering](#advanced-filtering)
- [Practice Scenario](#practice-scenario)
  - [Scenario:](#scenario)
    - [Task 1: Initial Network Assessment](#task-1-initial-network-assessment)
    - [Task 2: Configure Static IP Address](#task-2-configure-static-ip-address)
    - [Task 3: Configure Additional IP Address](#task-3-configure-additional-ip-address)
    - [Task 4: Network Troubleshooting](#task-4-network-troubleshooting)
- [Command Reference Cheat Sheet](#command-reference-cheat-sheet)
  - [nmcli Quick Reference](#nmcli-quick-reference)
  - [ip Command Quick Reference](#ip-command-quick-reference)
  - [ss Command Quick Reference](#ss-command-quick-reference)
- [Summary](#summary)


---

## Introduction
Networking management in RHEL 9 is done primarily with:
- **`nmcli`** → CLI tool for **NetworkManager** (manage connections/interfaces).
- **`ip`** → Show/modify IP addresses, routes, and interfaces.
- **`ss`** → Modern replacement for `netstat`, used to display socket statistics.


---

## NetworkManager with nmcli

### Key Concepts
- **Connections**: Configuration profiles for network interfaces
- **Devices**: Physical or virtual network interfaces
- **Active Connections**: Currently applied network configurations

### Essential Commands

#### View Network Status
```bash
# Show all devices and their status
nmcli device status

# Show all connections
nmcli connection show

# Show active connections
nmcli connection show --active
```

#### Manage Ethernet Connections
```bash
# Add a new static Ethernet connection
nmcli connection add con-name "eth0-static" ifname eth0 type ethernet \
    ip4 192.168.1.50/24 gw4 192.168.1.1
# The parameter autoconnect yes OR no, can be used to autoconnect the connection. If you have multiple connections with 'autoconnect yes', the interface can be confused about which connection to choose if the priority is not defined.   
# Creates a connection named my-eth0 for interface eth0.
# Assigns IP 192.168.1.50 with gateway 192.168.1.1.

# Add DHCP connection
nmcli connection add con-name "eth0-dhcp" ifname eth0 type ethernet

# Modify existing connection
nmcli connection modify "eth0-static" ipv4.dns "8.8.8.8,8.8.4.4"

# Bring connection up/down
nmcli connection up "eth0-static"
nmcli connection down "eth0-static"

# Delete connection
nmcli connection delete "eth0-static"
```

#### Real-time Monitoring
```bash
# Monitor network changes
nmcli monitor

# Show device details
nmcli device show eth0
```

## IP Command Suite

### Key Concepts
- `link`: Network interface management
- `addr`: IP address management
- `route`: Routing table management
- `neigh`: Neighbor (ARP) table management

### Essential Commands

#### Interface Management (ip link)
```bash
# Show all network interfaces
ip link show

# Bring interface up/down
ip link set eth0 up
ip link set eth0 down

# Show interface statistics
ip -s link show eth0
```

#### IP Address Management (ip addr)
```bash
# Show IP addresses for all interfaces
ip addr show

# Add IP address to interface
ip addr add 192.168.1.100/24 dev eth0

# Remove IP address from interface
ip addr del 192.168.1.100/24 dev eth0

# Flush all IP addresses from interface
ip addr flush dev eth0
```

#### Routing Management (ip route)
```bash
# Show routing table
ip route show

# Add default gateway
ip route add default via 192.168.1.1

# Add specific route
ip route add 10.0.0.0/8 via 192.168.1.254

# Delete route
ip route del default via 192.168.1.1
```

#### Neighbor Management (ip neigh)
```bash
# Show ARP table
ip neigh show

# Flush ARP cache
ip neigh flush all
```

## Socket Statistics with ss

### Key Concepts
- **Replacement for netstat**: Faster and more efficient
- **Various filters**: State, protocol, port-based filtering
- **Detailed socket information**: Comprehensive connection details

### Essential Commands

#### Basic Socket Information
```bash
# Show all listening and non-listening sockets
ss -tuln

# Show all TCP sockets
ss -t

# Show all UDP sockets
ss -u

# Show all listening sockets
ss -l
```

#### Advanced Filtering
```bash
# Show established TCP connections
ss -t state established

# Show connections to specific port
ss -t sport = :80
ss -t dport = :443

# Show connections from specific IP
ss -t src 192.168.1.100

# Useful for finding which service is using port 80
ss -tulpn | grep :80

# Show all HTTP connections
ss -t sport = :80
# OR
ss -t dport = :80

# Monitor connections in real-time
watch -n 1 'ss -t'

# Show processes using sockets
ss -tulp
```
- `-t` → TCP
- `-u` → UDP
- `-l` → listening sockets
- `-n` → numeric output


## Practice Scenario

### Scenario: 
Your RHEL 9 server needs a **static IP** and you must troubleshoot network connectivity.

#### Task 1: Initial Network Assessment
```bash
# 1. Check current network interfaces
ip link show

# 2. View current IP addresses
ip addr show

# 3. Check routing table
ip route show

# 4. Verify listening services
ss -tuln
```

#### Task 2: Configure Static IP Address
```bash
# 1. Create static connection with nmcli
nmcli connection add con-name "corp-static" ifname eth0 type ethernet \
    ip4 192.168.10.50/24 \
    gw4 192.168.10.1 \
    ipv4.dns "192.168.10.10,8.8.8.8" \
    ipv4.dns-search "corp.example.com"

# 2. Activate the connection
nmcli connection up "corp-static"

# 3. Verify configuration
nmcli connection show "corp-static"
ip addr show eth0
```

#### Task 3: Configure Additional IP Address
```bash
# Add secondary IP address using ip command
ip addr add 192.168.10.51/24 dev eth0 label eth0:0

# Verify the additional address
ip addr show eth0
```

#### Task 4: Network Troubleshooting
```bash
# 1. Check ARP table
ip neigh show

# 2. Trace route to gateway
traceroute 192.168.10.1

# 3. Check network statistics
ip -s link show eth0

# 4. Monitor real-time connections
watch -n 2 'ss -t state established'
```

## Command Reference Cheat Sheet

### nmcli Quick Reference
```bash
nmcli device status                  # Show device status
nmcli connection show               # List all connections
nmcli connection up "connection"    # Activate connection
nmcli connection down "connection"  # Deactivate connection
nmcli connection modify             # Modify connection properties
nmcli connection delete             # Delete connection
```

### ip Command Quick Reference
```bash
ip link show                        # Show interfaces
ip addr show                        # Show IP addresses
ip route show                       # Show routing table
ip neigh show                       # Show ARP table
ip addr add IP/MASK dev INTERFACE   # Add IP address
ip route add NETWORK via GATEWAY    # Add route
```

### ss Command Quick Reference
```bash
ss -tuln                            # All listening sockets
ss -t state established             # Established TCP connections
ss -t sport = :PORT                 # Connections from source port
ss -t dport = :PORT                 # Connections to destination port
ss -t src IP                        # Connections from specific IP
ss -tulp                            # Show processes with sockets
```


## Summary
- `nmcli` → Create/manage persistent network connections.
- `ip` → Assign/view IPs and routes (temporary changes).
- `ss` → Monitor sockets and check which ports/services are open.