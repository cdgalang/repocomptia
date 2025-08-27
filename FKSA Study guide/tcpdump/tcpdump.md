# Useful & Important tcpdump Commands

This is a cheat sheet of **tcpdump** commands, focused on packet capture, reading `.pcap` files, and analyzing with tools like `grep`, `awk`, `cut`, `sort`, and `uniq`.

---

## 🔹 Basic tcpdump Capture

- Capture all traffic on interface `eth0`:
  ```bash
  sudo tcpdump -i eth0
  ```

- Capture only first 50 packets:
  ```bash
  sudo tcpdump -i eth0 -c 50
  ```

- Capture and save to file:
  ```bash
  sudo tcpdump -i eth0 -w capture.pcap
  ```

- Read from a pcap file:
  ```bash
  tcpdump -r capture.pcap
  ```

---

## 🔹 Filters

- Capture only TCP:
  ```bash
  tcpdump tcp
  ```

- Capture only UDP:
  ```bash
  tcpdump udp
  ```

- Capture by port (e.g., 80 for HTTP):
  ```bash
  tcpdump port 80
  ```

- Capture traffic from a specific host:
  ```bash
  tcpdump host 192.168.1.10
  ```

- Capture between two hosts:
  ```bash
  tcpdump host 192.168.1.10 and host 192.168.1.20
  ```

- Capture source IP only:
  ```bash
  tcpdump src 192.168.1.10
  ```

- Capture destination IP only:
  ```bash
  tcpdump dst 192.168.1.20
  ```

---

## 🔹 Useful Options

- `-n` → Don’t resolve hostnames (faster).
- `-nn` → Don’t resolve hostnames or ports.
- `-v`, `-vv`, `-vvv` → Increase verbosity.
- `-X` → Show hex + ASCII.
- `-XX` → Show packet with Ethernet header.
- `-A` → Show ASCII only (good for HTTP payloads).
- `-c <num>` → Stop after N packets.
- `-s 0` → Capture full packet (no truncation).

---

## 🔹 Analyzing with -r (offline pcap analysis)

- Read only first 20 packets:
  ```bash
  tcpdump -r capture.pcap -c 20
  ```

- Only TCP packets:
  ```bash
  tcpdump -r capture.pcap tcp
  ```

- Only UDP packets:
  ```bash
  tcpdump -r capture.pcap udp
  ```

- Only ICMP packets:
  ```bash
  tcpdump -r capture.pcap icmp
  ```

---

## 🔹 Grep with tcpdump Output

- Find specific IP:
  ```bash
  tcpdump -r capture.pcap | grep "192.168.1.10"
  ```

- Find port 443 traffic:
  ```bash
  tcpdump -r capture.pcap | grep "443"
  ```

- Find ICMP only:
  ```bash
  tcpdump -r capture.pcap | grep "ICMP"
  ```

---

## 🔹 Awk / Cut for Field Extraction

Example tcpdump output:
```
12:34:56.789012 IP 192.168.1.10.12345 > 192.168.1.1.80: Flags [S], seq 1
```

- Extract source IPs:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $3}' | cut -d. -f1-4
  ```

- Extract destination IPs:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $5}' | cut -d. -f1-4
  ```

- Extract ports only:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $3}' | cut -d. -f5
  ```

---

## 🔹 Sorting & Counting (uniq tricks)

- Top talkers (source IPs):
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $3}' | cut -d. -f1-4 | sort | uniq -c | sort -nr
  ```

- Top destinations:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $5}' | cut -d. -f1-4 | sort | uniq -c | sort -nr
  ```

- Most used ports:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $3}' | cut -d. -f5 | sort | uniq -c | sort -nr | head
  ```

---

## 🔹 Sed Tricks

- Remove ARP packets:
  ```bash
  tcpdump -r capture.pcap | sed '/ARP/d'
  ```

- Clean timestamps (show only flows):
  ```bash
  tcpdump -n -r capture.pcap | sed 's/^[0-9:.]* //'
  ```

---

## 🔹 Combined Analysis Examples

- Top source → destination pairs:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $3, $5}' | cut -d. -f1-4,6 --output-delimiter=":" | sort | uniq -c | sort -nr | head
  ```

- Count HTTP (port 80) packets:
  ```bash
  tcpdump -n -r capture.pcap | grep ".80:" | wc -l
  ```

- List all unique protocols:
  ```bash
  tcpdump -n -r capture.pcap | awk '{print $2}' | sort | uniq
  ```

---

##  Summary

With just `tcpdump` + `grep`, `awk`, `cut`, `sort`, and `uniq`, you can quickly summarize a pcap into:

- Who talked to who (IP pairs)
- Most active talkers
- Most used ports
- Protocol distribution
- Payload inspection (ASCII/Hex)
