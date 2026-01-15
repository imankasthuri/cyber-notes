# 🛡️ Nmap Ultimate Master Notes

A comprehensive guide for network discovery, port scanning, and performance tuning. 

---

## 📍 1. Local & Remote Network Discovery
*Find out which devices are alive on the network.*

### Local Network Scan
> **Example:** You are on `192.168.66.89` and scanning your own subnet `192.168.66.0/24`.
* `sudo nmap -sn 192.168.66.0/24` — **Discover live hosts:** Uses ARP to find devices; shows MAC addresses and vendors.

### Remote Network Scan
> **Example:** Scanning `10.10.10.0/24` or `192.168.1.0/24` from a different network.
* `nmap -sL 192.168.1.0/24` — **List Targets:** Just lists the IPs; no actual scan or packets sent.
* `sudo nmap -sn 192.168.1.0/24` — **Standard Discovery:** Discover live hosts; uses ARP (if local) + MAC/vendor info.
* `sudo nmap -sn 10.10.10.0/24` — **Remote Discovery:** Discover live hosts using ICMP + TCP probes.
* `sudo nmap -sn -PE 10.10.10.0/24` — **Classic Ping:** Forces only ICMP Echo (Classic ping style).
* `sudo nmap -sn -ps22,80,443 10.10.10.50` — **TCP SYN Ping:** Probes specific ports to find live hosts.

---

## 🔍 2. Port Scanning Techniques
*Identify open ports and services on a target.*



### Core Scans
* `nmap 192.168.124.211` — **Basic Scan:** Just find open ports.
* `nmap -sT 10.10.10.50` — **TCP Connect Scan:** Performs a full 3-way handshake (standard user).
* `sudo nmap -sS 10.10.10.50` — **TCP SYN Scan:** Sends SYN; sends RST after receiving SYN-ACK (Stealthy).
* `sudo nmap -sU 10.10.10.50` — **UDP Scan:** Sends UDP packets to identify UDP services.

### Port Selection
* `sudo nmap -F 10.10.10.50` — **Quick Scan:** Scans the 100 most common ports.
* `sudo nmap -p-25 10.10.10.50` — **Range:** Scans ports 1 through 25 only.
* `sudo nmap -p- 10.10.10.50` — **Full Scan:** Scans every port from 1 to 65535.
* `sudo nmap -sS -sU 10.10.10.50` — **Combined:** Scans both TCP and UDP top ports.
* `sudo nmap -sS -sU -p- 10.10.10.50` — **The Works:** TCP SYN + UDP on every single port.

---

## 🛠️ 3. Service & OS Intelligence
*Determine what software and operating system the target is using.*

* `nmap -O 192.168.124.211` — **OS Detection:** Guess the operating system/kernel version.
* `nmap -sS -O 192.168.124.211` — SYN scan + OS detection.
* `nmap -sV 192.168.124.211` — **Version Detection:** Get service name + exact software version.
* `nmap -sS -sV 192.168.124.211` — SYN scan + Version detection.
* `nmap -A 192.168.124.211` — **Aggressive Scan:** Includes OS detection, versioning, default scripts, and traceroute.
* `nmap -Pn 192.168.124.211` — **Skip Discovery:** Skip host discovery; important for targets that block pings.

---

## ⚡ 4. Timing & Stealth
*Control how fast or slow Nmap runs to avoid detection or network congestion.*

### Timing Templates (`-T`)
* `nmap -sS -T0 192.168.124.211` / `-T paranoid` — **Paranoid:** Minutes between probes (Max stealth).
* `nmap -sS -T1 target` / `-T sneaky` — **Sneaky:** ~15 seconds between probes (Very slow).
* `nmap -sS -T2 target` / `-T polite` — **Polite:** ~0.4 seconds between probes (Low impact).
* `nmap -sS -T3 target` — **Normal:** Default speed (Balanced).
* `nmap -sS -T4 target` — **Fast:** Aggressive (Recommended for most cases).
* `nmap -sS -T5 target` — **Insane:** Extremely fast (Risky; may miss ports).

### Advanced Performance Controls
* `--min-parallelism 50` / `--max-parallelism 1` — Control probe concurrency.
* `--min-parallelism 100 --max-parallelism 100` — Fixes parallelism at 100 probes.
* `--min-rate 1000` / `--max-rate 10` — Control packets per second.
* `--min-rate 300 --max-rate 500` — Maintain a constant packet speed range.
* `--max-rate 0.1` — Ultra stealth (1 packet every 10 seconds).
* `--host-timeout 5m` / `--host-timeout 90s` — Set time limit for a single host before giving up.

---

## 📝 5. Verbosity & Output Logging
*Track your scan and save results for documentation.*

### Verbosity (Console Detail)
* `-v` — **Level 1:** Shows progress and found ports in real-time.
* `-vv` — **Level 2:** Higher verbosity.
* `-vvv` / `-v4` — **Level 3+:** Even more detail.
* `-d` / `-dd` / `-d3` / `-d9` — **Debugging:** Technical logs for troubleshooting.

### Output Formats
* `-oN gateway.nmap` — **Normal format:** Human-readable text.
* `-oX gateway.xml` — **XML format:** For machine processing or reporting tools.
* `-oG gateway.gnmap` — **Grepable format:** One line per host for easy searching.
* `-oA gateway` — **ALL formats:** Creates `.nmap`, `.xml`, and `.gnmap` at once.

---

## 🚀 6. Real-World Pro-Combos
```bash
# Aggressive scan, skip discovery, all ports
nmap -sS -sV -Pn -p- 192.168.124.211

# Aggressive, fast timing, skip discovery, save all formats
nmap -A -T4 -Pn -oA full_scan_124 192.168.124.211

# Comprehensive local network scan with verbosity
nmap -sS -v -oA network_audit 192.168.1.0/24

# Debugging a specific target with versioning and OS
nmap -sV -O -d -oN troubleshooting.txt 10.10.10.50
