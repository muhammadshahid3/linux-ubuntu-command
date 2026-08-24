# Ubuntu Networking — Essential Commands (Deep Guide)

This document explains the most important Ubuntu/Linux networking troubleshooting commands in depth — what each command **can do**, what it **cannot do**, and **2-3 real examples** for each.

---

## 1. `ip` ⭐⭐⭐⭐⭐

The single most important networking command. Replaces the older `ifconfig`/`route`/`netstat`.

### What it can do
- View and manage IP addresses, interfaces, routes, and neighbors (ARP table)
- Bring interfaces up/down
- Add/delete routes

### What it cannot do
- Cannot capture packets at the traffic level (use `tcpdump` for that)
- Cannot resolve DNS names (use `dig`/`nslookup` for that)

### Sub-commands & Examples

**`ip addr`** — View IP addresses and interfaces
```bash
ip addr show
ip addr show eth0
ip -4 addr        # show only IPv4
```

**`ip link`** — View and change interface status (up/down)
```bash
ip link show
sudo ip link set eth0 up
sudo ip link set eth0 down
```

**`ip route`** — View and manage the routing table
```bash
ip route show
sudo ip route add 192.168.2.0/24 via 192.168.1.1
sudo ip route del 192.168.2.0/24
```

**`ip neigh`** — View the ARP/neighbor table
```bash
ip neigh show
ip neigh show dev eth0
```

---

## 2. `ping` ⭐⭐⭐⭐⭐

Basic connectivity test — checks whether a destination is reachable (uses the ICMP protocol).

### What it can do
- Confirm whether a host is reachable
- Measure latency (round-trip time)
- Detect packet loss

### What it cannot do
- Cannot check connectivity to a specific port (use `nc` or `curl` for that)
- If a firewall blocks ICMP, ping will fail even if the host is actually reachable

### Examples
```bash
ping 8.8.8.8              # connectivity test only (IP)
ping google.com            # tests both DNS and connectivity
ping -c 4 google.com       # send only 4 packets and stop (not an infinite loop)
```

---

## 3. `ss` ⭐⭐⭐⭐⭐

Used to check open ports, listening services, and active connections. The modern replacement for `netstat`.

### What it can do
- Show which process is listening on which port
- Show active TCP/UDP connections
- Show connection states (ESTABLISHED, LISTEN, etc.)

### What it cannot do
- Cannot check whether a port is open on a **remote** host — it only works on the local machine (use `nc` for remote checks)

### Flag meanings
| Flag | Meaning |
|------|---------|
| `-t` | TCP |
| `-u` | UDP |
| `-l` | Listening sockets |
| `-n` | Numerical addresses/ports (doesn't resolve names) |
| `-p` | Show process/PID |

### Examples
```bash
ss -tulnp                  # all listening TCP/UDP ports + processes
ss -tulnp | grep :80       # check specifically for port 80
ss -t state established    # show only established TCP connections
```

---

## 4. `curl` ⭐⭐⭐⭐⭐

Sends HTTP/HTTPS requests and is used for troubleshooting APIs/servers.

### What it can do
- Fetch data from any URL/API
- View HTTP headers and status codes
- Send POST/PUT requests (test APIs)

### What it cannot do
- Not a pure network-level (ping/port) test — it only works at the application layer (HTTP)
- If DNS itself isn't resolving, curl will also fail (the root cause is DNS, not curl)

### Examples
```bash
curl http://localhost              # check local server response
curl -I https://google.com         # view headers only (status code, server info)
curl -v https://google.com         # verbose mode — shows every step of the connection
curl -X POST -d '{"key":"val"}' https://api.example.com   # send a POST request to an API
```

---

## 5. `dig` ⭐⭐⭐⭐⭐

The most powerful tool for DNS troubleshooting.

### What it can do
- Resolve a domain to its IP address
- Query a specific DNS server directly (bypassing local DNS)
- View DNS records like MX, NS, TXT

### What it cannot do
- Doesn't test connectivity (only resolves DNS; whether the host is actually reachable is a separate matter)

### Examples
```bash
dig google.com                      # default DNS record (A record)
dig @8.8.8.8 google.com             # query Google's DNS server directly
dig google.com MX                   # view mail server (MX) records
dig +short google.com               # just the IP address, no extra info
```

---

## 6. `nslookup`

An older but simpler tool for checking DNS resolution.

### What it can do
- Check domain-to-IP resolution
- Perform reverse lookups (IP to domain)

### What it cannot do
- Doesn't offer as much detail/flexibility as `dig` (limited for advanced DNS debugging)

### Examples
```bash
nslookup google.com                 # basic DNS lookup
nslookup 8.8.8.8                    # reverse lookup (IP → hostname)
nslookup google.com 8.8.8.8         # use a specific DNS server
```

---

## 7. `traceroute`

Shows the route (and number of hops) a packet takes to reach its destination.

### What it can do
- Show the IP and latency of each hop (router)
- Help identify where network delay is occurring

### What it cannot do
- Intermediate routers often block ICMP/UDP, causing a hop to show `* * *` (this means the response was blocked, not necessarily that the hop is down)

### Install (if not available)
```bash
sudo apt install traceroute
```

### Examples
```bash
traceroute google.com               # default route trace
traceroute -n google.com            # without resolving hostnames (faster)
traceroute -I google.com            # trace using ICMP (more reliable on some networks)
```

---

## 8. `tracepath`

A lightweight version of `traceroute` — doesn't require root privileges.

### What it can do
- Show the hops in a route (without sudo)
- Detect MTU (packet size) issues

### What it cannot do
- Doesn't offer as many customization options as `traceroute`

### Examples
```bash
tracepath google.com
tracepath -n google.com             # numerical (faster) output
tracepath -b google.com             # show both hostname and IP
```

---

## 9. `hostname`

Used to check/set the system's hostname.

### What it can do
- Show the current hostname
- Change the hostname temporarily

### What it cannot do
- For permanent, system-wide changes `hostnamectl` is better (a change via `hostname` may reset on reboot)

### Examples
```bash
hostname                            # show current hostname
hostnamectl                         # full system info (hostname + OS + kernel)
sudo hostnamectl set-hostname newname   # permanently change the hostname
```

---

## 10. `resolvectl`

Used on Ubuntu (systemd-resolved based) systems to view DNS configuration/status.

### What it can do
- Show current DNS servers
- Show per-interface DNS settings
- Flush the DNS cache

### What it cannot do
- Won't work on systems not using systemd-resolved (e.g., some minimal server setups)

### Examples
```bash
resolvectl status                   # DNS status for all interfaces
resolvectl query google.com         # resolve a specific domain
sudo resolvectl flush-caches        # clear the DNS cache
```

---

## 11. `nmcli`

A connection management tool for systems using NetworkManager (mostly desktop Ubuntu).

### What it can do
- Manage WiFi/Ethernet connections
- Create new connections
- View connection status

### What it cannot do
- Won't work on server distros that don't use NetworkManager (e.g., Netplan-only setups)

### Examples
```bash
nmcli device status                 # connection status of all devices
nmcli connection show                # show all saved connections
nmcli general status                 # overall network status (connectivity, state)
```

---

## 12. `nc` — Netcat ⭐⭐⭐⭐

The "Swiss army knife" of networking — used for port connectivity testing and raw data transfer.

### What it can do
- Check whether a specific port on a remote host is open
- Build a simple TCP/UDP server/client for testing

### What it cannot do
- Doesn't show application-level details like HTTP headers/status codes — it only tests the raw connection

### Examples
```bash
nc -vz 192.168.1.10 80              # check if port 80 is open (verbose + zero-I/O)
nc -vz 192.168.1.10 20-100          # scan a range of ports
nc -l -p 1234                       # listen on port 1234 locally (for testing)
```

---

## 13. `telnet`

An older tool, originally built for remote terminal access, but nowadays mostly used to test whether a specific TCP port is open — similar to `nc`.

### What it can do
- Check whether a specific TCP port on a remote host is open
- Manually interact with plain-text protocols (like HTTP, SMTP) by typing commands after connecting

### What it cannot do
- Doesn't support UDP (unlike `nc`, which can test both TCP and UDP)
- Not secure for actual remote login — has been replaced by `ssh` for that purpose
- Not installed by default on many modern Ubuntu systems

### Install (if not available)
```bash
sudo apt install telnet
```

### Examples
```bash
telnet 192.168.1.10 80              # check if port 80 is open (manual HTTP test possible)
telnet google.com 443               # check if port 443 (HTTPS) is reachable
telnet mailserver.local 25          # test SMTP port connectivity, and manually issue SMTP commands
```

> **Tip:** For quick port checks, `nc -vz` is generally preferred since it's faster and doesn't leave an open interactive session — but `telnet` is still common in older environments and is worth knowing.

---

## 14. `tcpdump` ⭐⭐⭐⭐⭐

An advanced packet capture tool — captures and analyzes actual network traffic.

### What it can do
- Capture live network packets
- Filter traffic by port/protocol/IP
- Save captures to a file (for later analysis in Wireshark)

### What it cannot do
- Doesn't show the actual content of encrypted traffic (HTTPS) — only metadata (source/dest, size)

### Examples
```bash
sudo tcpdump -i any                          # show traffic on all interfaces
sudo tcpdump -i any port 80                  # show only port 80 (HTTP) traffic
sudo tcpdump -i eth0 -w capture.pcap         # save traffic to a file
sudo tcpdump -i any host 192.168.1.10        # show traffic for a specific IP only
```

---

## 15. `ethtool`

Used to view hardware-level details of a network interface (NIC).

### What it can do
- Show link speed and duplex mode
- Check whether a cable is connected

### What it cannot do
- Doesn't manage IP/routing configuration — it's purely for the hardware/link layer

### First, find the interface name
```bash
ip link
```

### Examples
```bash
sudo ethtool eth0                   # full interface details (speed, duplex)
sudo ethtool eth0 | grep Speed      # view link speed only
sudo ethtool -i eth0                # driver and firmware info
```

---

## 16. `route` / `netstat` — Legacy Commands

These are historical commands — still functional, but `ip` and `ss` are preferred on modern Linux.

### Old vs Modern Mapping
| Legacy | Modern |
|--------|--------|
| `route -n` | `ip route` |
| `netstat -tulnp` | `ss -tulnp` |
| `ifconfig` | `ip addr` |

### Examples (Legacy)
```bash
route -n                            # routing table (old style)
netstat -tulnp                      # listening ports (old style)
netstat -r                          # route table view (alternative)
```

---

## 🎯 Priority List — Learn These 10 First

If you're starting with just the **MOST IMPORTANT** commands:

1. `ip`
2. `ping`
3. `ss`
4. `curl`
5. `dig`
6. `nslookup`
7. `traceroute`
8. `resolvectl`
9. `nc`
10. `tcpdump`

### Master `ip` most deeply:
```
ip
├── ip addr    → IP/interfaces
├── ip link    → interface up/down status
├── ip route   → routing table
└── ip neigh   → ARP/neighbor table
```

---

## Troubleshooting Flow (Networking)

```
Problem
   ↓
Connectivity issue?      → ping, traceroute
   ↓
DNS issue?                → dig, nslookup, resolvectl
   ↓
Port/service issue?       → ss, nc
   ↓
Application/HTTP issue?   → curl
   ↓
Deep packet inspection?   → tcpdump
```

**Example real-world scenario:**

A website isn't loading:
```bash
ping google.com               # 1. basic connectivity check
dig google.com                # 2. is DNS resolving?
curl -I https://google.com    # 3. is an HTTP response coming back?
ss -tulnp | grep :443         # 4. is the local machine listening on the port?
sudo tcpdump -i any port 443  # 5. deep-dive: inspect the actual traffic
```

---

*Reference guide for essential Ubuntu/Linux networking commands — with capabilities, limitations, and practical examples for real-world troubleshooting.*
