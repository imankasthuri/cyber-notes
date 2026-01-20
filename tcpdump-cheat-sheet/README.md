# tcpdump: The Complete Command Masterlist
*Comprehensive guide for network analysis using real-world syntax and filters.*

---

## 1. Essential Setup & Operation
*Basic commands to control where you listen and how you save data.*

### Identify the Interface
* `ip a s` — Lists available interfaces (e.g., `ens5`, `eth0`).
* `sudo tcpdump -i ens5` — Capture on the `ens5` interface.
* `sudo tcpdump -i any` — Capture on all interfaces simultaneously.

### Display & Performance Controls
* `sudo tcpdump -i ens5 -c 5 -n` — Capture exactly **5 packets** and **don't resolve** DNS names.
* `sudo tcpdump -i any -nn` — Don't resolve IP addresses **OR** port numbers (e.g., shows `80` instead of `http`).
* `sudo tcpdump -v` — Verbose output (shows TTL, ID, and length). Use `-vv` or `-vvv` for more detail.

### Saving & Loading PCAPs
* `sudo tcpdump -i wlo1 -w data.pcap` — Save all traffic from `wlo1` to `data.pcap` (screen stays blank).
* `tcpdump -r traffic.pcap -c 5 -n` — Read the first 5 packets from `traffic.pcap` without DNS resolution.

---

## 2. Powerful Filtering Expressions
*Use these to isolate specific traffic in real scenarios.*

### Filtering by Host
* `sudo tcpdump host example.com -w http.pcap` — Capture everything exchanged with a specific domain.
* `tcpdump -r traffic.pcap src host 192.168.124.1 -n` — Read only packets **sent by** this IP.
* `sudo tcpdump dst host 10.11.81.126` — Capture only packets **going to** this destination.

### Filtering by Port & Protocol
* `sudo tcpdump -i ens5 port 53 -n` — Capture DNS traffic (Port 53).
* `sudo tcpdump -i any tcp port 22` — Capture all SSH traffic.
* `sudo tcpdump -i wlo1 udp port 123` — Capture NTP traffic (Time Protocol).
* `sudo tcpdump -i ens5 icmp -n` — Capture Ping (ICMP) echo requests/replies.

### Combining Filters (Logical Operators)
* **AND:** `tcpdump host 1.1.1.1 and tcp` — Both must be true.
* **OR:** `tcpdump udp or icmp` — Either condition is true.
* **NOT:** `tcpdump not tcp` — Everything except TCP (shows UDP, ICMP, ARP).
* **Combo:** `tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap`

---

## 3. Advanced Binary & Flag Filtering
*Deep packet inspection using bitwise operations.*



### TCP Flag Filters
* `tcpdump "tcp[tcpflags] == tcp-syn"` — Capture ONLY **SYN** (new connection) packets.
* `tcpdump "tcp[tcpflags] & tcp-syn != 0"` — Capture packets with **at least** the SYN flag set.
* `tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"` — Capture SYN **or** ACK packets.

### Raw Byte Offsets
* `ether[0] & 1 != 0` — Filters packets sent to a multicast address.
* `ip[0] & 0xf != 5` — Catches all IP packets that contain "options."
* `greater 1000` — Filters packets with length greater than or equal to 1000 bytes.
* `less 64` — Filters packets with length less than or equal to 64 bytes.

---

## 4. Formatting & Inspection
*Change how the raw data is displayed in your terminal.*

* `tcpdump -r TwoPackets.pcap -q` — **Quick:** Brief info (IPs, Ports, and Length only).
* `tcpdump -r TwoPackets.pcap -e` — **Ethernet:** Shows **MAC addresses**.
* `tcpdump -r TwoPackets.pcap -A` — **ASCII:** Shows data as readable text (Best for HTTP/Plan-text).
* `tcpdump -r TwoPackets.pcap -xx` — **Hex:** Shows raw data in hexadecimal.
* `tcpdump -r TwoPackets.pcap -X` — **Hex + ASCII:** Best of both worlds (Hex on left, Text on right).

---

## 5. Real-World One-Liners

```bash
# Count how many packets came from a specific host in a capture file
tcpdump -r traffic.pcap src host 192.168.124.1 -n | wc -l

# Watch HTTPS traffic for a specific host in Hex/ASCII format
sudo tcpdump -i eth0 host example.com and tcp port 443 -X

# Look for ICMP time exceeded (Traceroute) and Unreachable errors
sudo tcpdump -i ens5 icmp -n -vv
