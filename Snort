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
