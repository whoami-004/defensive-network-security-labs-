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
