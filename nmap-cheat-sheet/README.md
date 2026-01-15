# 🛡️ The Complete Nmap Master Reference Guide
*An exhaustive collection of Nmap commands for local and remote network auditing.*

---

## 📍 1. Network Discovery (Finding Live Hosts)
*Use these to identify which IP addresses are active on a network.*

### Local Subnet (Layer 2)
*Relies on ARP requests. Best for networks you are physically connected to.*
* `sudo nmap -sn 192.168.66.0/24` — **Local Scan:** Finds live hosts on your current subnet.

### Remote Subnet (Layer 3)
*Relies on ICMP and TCP probes to bypass routing.*
* `nmap -sL 192.168.1.0/24` — **List Scan:** Simply lists targets without sending packets.
* `sudo nmap -sn 192.168.1.0/24` — **Standard Discovery:** Uses ARP (if local) and shows MAC/Vendor.
* `sudo nmap -sn 10.10.10.0/24` — **Remote Discovery:** Uses ICMP Echo + TCP ACK/SYN probes.
* `sudo nmap -sn -PE 10.10.10.0/24` — **Classic Ping:** Forces ICMP Echo requests only.
* `sudo nmap -sn -PS22,80,443 10.10.10.50` — **Port-Specific Discovery:** TCP SYN ping on selected ports.

---

## 🔍 2. Port Scanning Techniques
*Identify which services are listening on specific ports.*

* `nmap 192.168.124.211` — **Basic Scan:** Finds open ports using default settings.
* `nmap -sT 10.10.10.50` — **TCP Connect:** Full 3-way handshake (standard user).
* `sudo nmap -sS 10.10.10.50` — **TCP SYN Scan:** "Stealth" half-open scan (root default).
* `sudo nmap -sU 10.10.10.50` — **UDP Scan:** Scans for UDP-based services.
* `sudo nmap -sS -sU 10.10.10.50` — **Combined:** Scans both TCP and UDP top ports.

### Port Selection Range
* `sudo nmap -F 10.10.10.50` — **Fast:** Scans the 100 most common ports.
* `sudo nmap -p1-25 10.10.10.50` — **Range:** Scans ports 1 through 25 only.
* `sudo nmap -p- 10.10.10.50` — **Full:** Scans all 65,535 ports.
* `sudo nmap -sS -sU -p- 10.10.10.50` — **The Works:** TCP SYN + UDP on every single port.

---

## 🛠️ 3. Service & OS Intelligence
*Determine what is running and what the operating system is.*

* `nmap -O 192.168.124.211` — **OS Detection:** Attempts to identify the OS kernel/version.
* `nmap -sV 192.168.124.211` — **Service Version:** Gets service name + exact software version.
* `nmap -A 192.168.124.211` — **Aggressive:** Includes OS, Version, Default Scripts, and Traceroute.
* `nmap -Pn 192.168.124.211` — **No-Ping:** Skips host discovery; essential for targets that block ICMP.

---

## ⚡ 4. Timing & Performance Control
*Control how fast or stealthy the scan runs.*

### Timing Templates (`-T`)
* `-T0` / `-T paranoid` — **Paranoid:** Minutes between probes (Extreme stealth).
* `-T1` / `-T sneaky` — **Sneaky:** ~15 seconds between probes.
* `-T2` / `-T polite` — **Polite:** ~0.4 seconds between probes (Low bandwidth).
* `-T3` — **Normal:** The default balanced speed.
* `-T4` / `-T aggressive` — **Aggressive:** Fast and reliable for modern networks.
* `-T5` / `-T insane` — **Insane:** Extremely fast; risks missing ports or triggering alarms.

### Advanced Performance Tweaks
* `--min-parallelism [num]` — Force a minimum number of simultaneous probes.
* `--max-parallelism [num]` — Limit probes (e.g., `--max-parallelism 1` for ultra-slow).
* `--min-rate [num]` — Send packets no slower than [num] per second.
* `--max-rate [num]` — Cap packet speed (e.g., `--max-rate 0.1` for 1 packet every 10s).
* `--host-timeout [time]` — Give up on a slow host after a set time (e.g., `90s` or `5m`).

---

## 📝 5. Verbosity & Output Logging
*Monitor scan progress and save results for later.*

### Verbosity (How much info you see)
* `-v` — **Level 1:** Shows open ports as they are found.
* `-vv` / `-vvv` — **Higher Detail:** Shows more internal progress.
* `-d` / `-dd` / `-d9` — **Debugging:** Technical logs (Level 1 to 9).

### Saving Results
* `-oN gateway.nmap` — **Normal:** Standard text format.
* `-oX gateway.xml` — **XML:** For machine reading or importing to other tools.
* `-oG gateway.gnmap` — **Grepable:** One line per host for easy searching.
* `-oA filename` — **All Formats:** Saves in `.nmap`, `.xml`, and `.gnmap` at once.

---

## 🚀 6. Ultimate Combination Examples
*Commonly used "Pro" commands.*

```bash
# Aggressive scan, skip ping, save all formats
nmap -A -Pn -oA full_scan_results 192.168.124.211

# The "CTF" Special: Fast, Service Versions, All Ports, No Ping
nmap -sS -sV -Pn -p- -T4 192.168.124.211

# Maximum Speed Discovery
nmap --min-rate 1000 -T5 192.168.1.0/24

# The "Stealth Detective"
nmap -sS -sV -O -T2 192.168.124.211
