# 🛡️ Snort Notes – IDS/IPS Cheatsheet

## Snort Modes

### 1. Sniffer Mode
Captures and displays real-time network packets.

```bash
sudo snort -i <interface>               # Basic sniffing
sudo snort -i <interface> -e           # Includes Ethernet headers
sudo snort -i <interface> -d           # Shows application layer data
sudo snort -i <interface> -X           # Hex + ASCII view
```

---

### 2. Packet Logger Mode
Logs packets for later analysis.

```bash
mkdir logs
cd logs
sudo snort -i <interface> -l .
```

---

### 3. NIDS/NIPS Mode
Detects attacks using rules defined in `snort.conf`.

```bash
sudo snort -c /etc/snort/snort.conf -i <interface>
```

---

## Required Libraries

| Library | Description                         |
|---------|-------------------------------------|
| libpcap | Captures live packets               |
| PCRE    | Regex engine for matching rules     |
| ZLIB    | Compresses/decompresses payloads    |

---

## Configuring Snort

### Step 1: Backup Config
```bash
cp /etc/snort/snort.conf /etc/snort/snort.conf.bak
```

### Step 2: Edit Config
```bash
sudo nano /etc/snort/snort.conf
```

- Example change:
```conf
ipvar HOME_NET 192.168.1.0/24
```

### Step 3: Test Config
```bash
sudo snort -T -c /etc/snort/snort.conf
```

---

## Writing Snort Rules

### Rule Syntax

```text
action protocol src_IP src_port -> dst_IP dst_port (rule options)
```

### Rule Keywords

| Keyword        | Description                                 |
|----------------|---------------------------------------------|
| `sid`          | Snort rule ID (use > 1,000,000 for custom)  |
| `rev`          | Rule revision number                        |
| `content`      | Match specific string or pattern            |
| `nocase`       | Case-insensitive matching                   |
| `http_uri`     | Match in HTTP URI                           |
| `http_header`  | Match in HTTP header                        |

---

## Sample Snort Alert Rules

### Rule 1: `.exe` in URI
```snort
alert tcp any any -> any 80 (msg:"Requested Port 80"; content:"|2e|exe"; nocase; http_uri; sid:1000001; rev:1;)
```

### Rule 2: Download via HTTP Header
```snort
alert tcp any 80 <> any any (msg:"Download Detected"; content:"Content-Type: application/x-msdownload"; http_header; sid:1000002; rev:1;)
```

### Rule 3: MZ Signature
```snort
alert tcp any 80 <> any any (msg:"MZ Signature Match"; file_data; content:"|4D 5A|"; depth:2; sid:1000003; rev:1;)
```

### Rule 4: Metasploit Port 4444 (SYN)
```snort
alert tcp any any -> any 4444 (msg:"Metasploit Backdoor Detected"; flags:S; sid:1000004; rev:1;)
```

### Rule 5: SSLoad User-Agent Detection
```snort
alert tcp any any <> any any (msg:"SSLoad User-Agent Detected"; content:"SSLoad/1.1"; http_header; nocase; sid:1000005; rev:1;)
```

### Rule 6: SSH Brute Force
```snort
alert tcp any any -> any 22 (msg:"SSH Brute Force Detected"; flow:to_server,established; threshold:type both, track by_src, count 5, seconds 30; sid:1000006; rev:1;)
```

---

## Snort as IPS (Inline Mode)

### Rule: Drop FTP Traffic
```snort
drop tcp any any <> any 21 (msg:"Drop FTP"; sid:1000007; rev:1;)
```

### Run Snort Inline (IPS Mode)
```bash
sudo snort -q -A console -l /var/log/snort -i enp0s3:enp0s8 -c /etc/snort/snort.conf -Q
```

Or with DAQ explicitly:
```bash
sudo snort -q -A console -l /var/log/snort -i enp0s3:enp0s8 -c /etc/snort/snort.conf --daq afpacket -Q
```

---

## Reading PCAPs and Logs

### Read PCAP File and Show Alerts
```bash
sudo snort -c /etc/snort/snort.conf -q -r 1.pcap -A console
```

### Read Snort Log
```bash
sudo snort -r /var/log/snort/snort.log.1736620546 -q -d
```

---

##  Testing Rules

### Simulate Metasploit Traffic with hping3
```bash
sudo hping3 -c 1 -p 4444 -S example.com
```

---

## PCAP Analysis with Wireshark

- Use **Statistics → Protocol Hierarchy** to identify suspicious traffic (e.g., SSH brute force).
- Use in combination with Snort threshold rules.

---

## Snorpy – Rule Generator
Create rules visually:
👉 [Snorpy Rule Generator](https://anir0y.in/snort2-rulgen/)

---

# TCPDUMP COMMAND REFERENCE

## Interface Commands

- **List available interfaces**  
  `tcpdump -D`

- **Capture packets from loopback interface with Hex + ASCII**  
  `sudo tcpdump -i lo -X`

- **Capture packets from loopback interface in ASCII only**  
  `sudo tcpdump -i lo -A`

## Interface + Filter Commands

- **Default capture on specific interface**  
  `sudo tcpdump -i <network interface>`

- **Disable DNS and service name resolution**  
  `sudo tcpdump -i <network interface> -n`

- **Capture packets to/from a specific host**  
  `sudo tcpdump -i <network interface> -n host <ip address>`

- **Filter by source IP**  
  `sudo tcpdump -i <network interface> -n src <ip address>`

- **Filter by destination IP**  
  `sudo tcpdump -i <network interface> -n dst <ip address>`

- **Capture traffic from a specific network**  
  `sudo tcpdump -i <network interface> -n net <network>/<CIDR>`

- **Filter by port**  
  `sudo tcpdump -i <network interface> -n port <port number>`

- **Filter by source port**  
  `sudo tcpdump -i <network interface> -n src port <port number>`

- **Filter by IP and source port**  
  `sudo tcpdump -i <network interface> -n src <ip address> and src port <port number>`

- **Exclude a specific port**  
  `sudo tcpdump -i <network interface> -n src <ip address> and not port <port number>`

- **Complex filter: source, destination, and port exclusion**  
  `sudo tcpdump -i <network interface> -n 'src <ip> and dst <ip> and not (port <port1> or port <port2>)'`

- **Capture only ICMP traffic (e.g., ping)**  
  `sudo tcpdump -i <network interface> -n icmp`

- **Capture only UDP traffic**  
  `sudo tcpdump -i <network interface> -n udp`

- **Capture only TCP traffic**  
  `sudo tcpdump -i <network interface> -n tcp`

- **Capture only ARP traffic**  
  `sudo tcpdump -i <network interface> -n arp`

## Write & Read Pcap Files

- **Save packets to a file**  
  `sudo tcpdump -i <network interface> -n -w ~/Desktop/output.pcap`

- **Read packets from a saved file**  
  `tcpdump -r ~/Desktop/output.pcap`

- **Count packets in a pcap file**  
  `tcpdump -r <file.pcap> --count`

- **Read first N packets only**  
  `tcpdump -r <file.pcap> -c <number>`

- **Read without timestamps**  
  `tcpdump -r <file.pcap> -t`

- **Show timestamps in raw seconds**  
  `tcpdump -r <file.pcap> -tt`

- **Show time delta between packets**  
  `tcpdump -r <file.pcap> -ttt`

- **Show full date and time**  
  `tcpdump -r <file.pcap> -tttt`

## Pcap Investigation Examples

- **Show packets with timestamps**  
  `tcpdump -tt -r <file.pcap>`

- **Filter HTTP GET/POST traffic**  
  `tcpdump -r <file.pcap> -tt port 80 | grep -E "GET|POST"`

- **Search for malicious .exe downloads**  
  `tcpdump -r <file.pcap> -tt port 80 | grep -E "<filename>.exe"`

- **Extract payload lines after suspicious file**  
  `tcpdump -r <file.pcap> -tt -A | grep -E "<filename>.exe" -A <number> | less`

## IP & Port Statistics

- **Count unique source IPs**  
  `tcpdump -tt -r <file.pcap> -n tcp | cut -d " " -f 3 | cut -d "." -f 1-4 | sort | uniq -c | sort -nr`

- **Count unique destination IPs**  
  `tcpdump -tt -r <file.pcap> -n tcp | cut -d " " -f 5 | cut -d "." -f 1-4 | sort | uniq -c | sort -nr`

- **Find ports used between IP pairs**  
  `tcpdump -tt -r <file.pcap> -n 'tcp and src <ip> and dst <ip>' | cut -d " " -f 3 | cut -d "." -f 5 | sort | uniq -c | sort -nr`

## Behavioral + Malware Indicators

- **Detect HTTP GET/POST between suspicious hosts**  
  `tcpdump -tt -r <file.pcap> src <ip> and dst <ip> | grep -E "GET|POST"`

- **Extract ASCII content from suspicious traffic**  
  `tcpdump -tt -r <file.pcap> src <ip> and dst <ip> -c <number> -A`

- **Find known malicious User-Agent strings**  
  `tcpdump -tt -r <file.pcap> | grep "User-Agent: <malicious-agent>"`

- **Capture all traffic to/from suspicious IP**  
  `tcpdump -tt -r <file.pcap> host <ip>`

- **Search for exposed credentials**  
  `tcpdump -tt -r <file.pcap> host <ip> -A | grep -i 'user\|pass\|login' | grep -v User-Agent`

- **Search for suspicious filenames**  
  `tcpdump -tt -r <file.pcap> host <ip> -A | grep "filename"`

- **Look for Telegram C2 links**  
  `tcpdump -tt -r <file.pcap> | grep "t.me"`

- **Scan for DLL file mentions**  
  `tcpdump -tt -r <file.pcap> | grep dll -A <number>`

## Analysis Checklist

- Inspect all GET and POST requests  
- Look for unknown or suspicious User-Agents  
- Trace IPs with repeated malicious behavior  
- Check for `.exe`, `.dll`, or hidden file references  
- Look for Telegram links or C2 indicators  
- Decode suspicious URLs and scan with VirusTotal

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
