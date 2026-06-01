# 🕵️‍♂️ Wireshark Analysis Cheat Sheet

A complete reference guide for analyzing packet captures using **Wireshark**.

---

## Capture Filters (Before Capture)

Use **Capture Filters** to filter packets while capturing traffic (more efficient than Display Filters).

- **Capture traffic to/from a specific IP**  
  `host <ip address>`

- **Capture traffic to/from a subnet**  
  `net <ip address>/24`

- **Capture traffic from a subnet**  
  `src net <ip address>/24`

- **Capture traffic to a subnet**  
  `dst net <ip address>/24`

- **Capture DNS traffic**  
  `port 53`

- **Exclude HTTP and SMTP**  
  `host www.example.com and not (port 80 or port 25)`

- **Exclude ARP and DNS**  
  `port not 53 and not arp`

- **Capture TCP port range**  
  `tcp portrange 1501-1549`

- **Capture EAPOL traffic**  
  `ether proto 0x888e`

- **Exclude LLDP multicast**  
  `not ether dst 01:80:c2:00:00:0e`

- **Capture only IPv4**  
  `ip`

- **Capture only unicast traffic**  
  `not broadcast and not multicast`

- **Capture IPv6 all-nodes multicast**  
  `dst host ff02::1`

- **Capture HTTP GET requests**  
  `port 80 and tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420`

---

## 🧪 Display Filters (After Capture)

Use **Display Filters** to inspect specific packet details *after* capture.

- **Traffic to/from a specific IP**  
  `ip.addr == <ip address>`

- **Traffic from subnet**  
  `ip.src == <ip address>/24`

- **Traffic to subnet**  
  `ip.dst == <ip address>/24`

- **Only DNS traffic**  
  `dns`

- **Exclude HTTP and SMTP**  
  `!(http || smtp)`

- **Exclude ARP and DNS**  
  `!(arp || dns)`

- **TCP port range**  
  `tcp.port >= 1501 && tcp.port <= 1549`

- **Only EAPOL traffic**  
  `eth.type == 0x888e`

- **Exclude LLDP multicast**  
  `eth.dst != 01:80:c2:00:00:0e`

- **Only IPv4 traffic**  
  `ip`

- **Only unicast traffic**  
  `!(eth.dst[0] & 1)`

- **IPv6 all-nodes traffic**  
  `ipv6.dst == ff02::1`

- **HTTP GET only**  
  `http.request.method == "GET"`

---

##  Display Filter Expressions

Useful advanced expressions for deep packet inspection:

### Basic Examples

- Match IP  
  `ip.addr == <ip address>`

- Frame size greater than 10  
  `frame.len > 10`

- Match domains using regex  
  `http.host matches "acme\\.(org|com|net)"`

### Logical Operators

- Match SYN flag from a specific IP  
  `ip.src == <ip address> and tcp.flags.syn == 1`

- Match multiple HTTP methods  
  `http.request.method in {"GET", "POST"}`

### Slice Operators

- Match MAC prefix  
  `eth.src[0:3] == 00:00:83`

- Match last 4 bytes  
  `frame[-4:4] == 0.1.2.3`

### Binary Match

- TCP SYN check  
  `tcp.flags & 0x02`

- UDP contains  
  `udp contains 81:60:03`

- SIP field match  
  `sip.To contains "a1762"`

---

## Useful Wireshark Tools

| Tool | Purpose |
|------|---------|
| **Protocol Hierarchy** | View all detected protocols |
| **Conversations** | See IP/MAC pair communications |
| **Endpoints** | View devices (IPs, MACs) |
| **Resolved Addresses** | DNS name mapping |
| **Export Objects** | Save transferred files (HTTP, SMB, FTP) |
| **HTTP Statistics** | Count methods, requests |

---

## Wireshark Malware Analysis Workflow

1. Open `.pcap` in Wireshark  
2. Go to **Statistics → Protocol Hierarchy**  
3. Analyze **Endpoints**, **Conversations**  
4. Add useful columns:
- `tcp.stream`, `ip.src`, `ip.dst`, `http.host`  
5. Use filters:
6. Export files:
- `File → Export Objects → HTTP`  
7. Get hash:
- `sha256sum file`  
8. Upload hash to [VirusTotal](https://www.virustotal.com)  
9. Decode malware strings using [CyberChef](https://gchq.github.io/CyberChef/)  
10. Analyze protocols like:
 - `http`, `smb`, `ftp`, `dns`, `icmp`

---

## Practice Lab

Practice malware traffic analysis using **real-world PCAPs** here:

👉 [https://www.malware-traffic-analysis.net/](https://www.malware-traffic-analysis.net/)

---

> 🔁 Always replace `<ip address>` with your actual target like `10.129.203.6`
