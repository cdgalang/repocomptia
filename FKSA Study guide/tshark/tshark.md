# tshark `-Y` Display Filter Cheat Sheet

A compact reference for common Wireshark/tshark **display filters** (`-Y`) you’ll actually use. Copy/paste into your GitHub notes.

---

## Core Patterns
```text
# equality / inequality
ip.src == 10.0.0.5
dns.qry.name != "example.com"

# existence (field present / boolean true)
http.request
tcp.analysis.retransmission

# substring / regex
http.host contains "login"
http.request.uri matches "(?i)\.php($|\?)"

# sets / ranges
frame.number in {1 5-10 42}
tcp.port in {80 443 8080-8090}

# combine
(ip.addr == 10.0.0.5 && tcp) || (udp.port == 53)
!(arp || icmp)
```

## Frame & Link Layer
```text
frame.number == 123
frame.len > 1500
frame.time_relative > 1.0
frame.time_delta > 0.1
eth.src == aa:bb:cc:dd:ee:ff
eth.dst == ff:ff:ff:ff:ff:ff
```

## IP
```text
ip
ip.addr == 192.168.1.50
ip.dst == 10.0.0.0/24
ip.ttl < 5
(ip.flags.mf == 1) || (ip.frag_offset > 0)   # fragmented
```

## TCP / UDP
```text
tcp
tcp.port == 443
tcp.srcport == 22 || tcp.dstport == 22
tcp.flags.syn == 1 && tcp.flags.ack == 0     # SYNs
tcp.flags.reset == 1                         # RSTs
tcp.len > 0                                  # has payload
tcp.stream == 7                              # a specific TCP conversation
tcp.analysis.retransmission || tcp.analysis.duplicate_ack

udp
udp.port == 53
udp.stream == 3
```

## HTTP
```text
http
http.request                                  # any request
http.response                                 # any response
http.request.method == "POST"
http.host contains "example.com"
http.request.uri contains ".php"
http.user_agent matches "(?i)curl|wget"
http.cookie
http.set_cookie
http.response.code == 404
```

## DNS
```text
dns
dns.flags.response == 1                        # responses
dns.flags.rcode != 0                           # errors (NXDOMAIN, etc.)
dns.qry.name == "example.com"
dns.a == 93.184.216.34
dns.aaaa
```

## TLS (SSL)
```text
tls
tls && tcp.port == 443
tls.handshake.type == 1                        # ClientHello
tls.handshake.type == 2                        # ServerHello
tls.handshake.extensions_server_name == "example.com"   # SNI
tls.record.content_type == 23                  # application_data
tls.alert_message
```

## ARP / ICMP / DHCP
```text
arp
arp.opcode == 1                                # request
arp.opcode == 2                                # reply
arp.src.proto_ipv4 == 192.168.1.10

icmp
icmp.type == 8 && icmp.code == 0               # echo request
icmp.type == 0 && icmp.code == 0               # echo reply

bootp                                           # DHCPv4 (alias)
dhcpv6
```

## Wi‑Fi (802.11)
```text
wlan
wlan.fc.type_subtype == 0x08                   # Beacons
wlan.sa == aa:bb:cc:dd:ee:ff                   # source (AP/client)
wlan.da == ff:ff:ff:ff:ff:ff                   # broadcast
wlan.ssid == "MyWiFi"
```

## Handy Hunting Combos
```text
# Any traffic to/from host on web ports
(ip.addr == 10.0.0.5) && tcp.port in {80 443}

# HTTP POSTs with credential-ish strings
http.request.method == "POST" && http contains "password"

# Slow/chatty TCP (gaps >100ms or retransmits)
tcp.analysis.retransmission || frame.time_delta > 0.1

# DNS errors or long CNAME chains
dns.flags.rcode != 0 || dns.count.answers > 10
```

---

### Tips
- Enumerate filterable fields: `tshark -G fields | less -S`
- Only names (clean): `tshark -G fields | cut -f1`
- Protocol-only list: `tshark -G protocols`
- Values/enums catalog: `tshark -G values`
- Use with reading a pcap: `tshark -r file.pcap -Y "<filter>"`

> Bonus: Extract values instead of filtering with `-T fields -e <field>` and control formatting with `-E header=y -E separator=,` (CSV output).


---

# Field Extraction: `-T fields -e ...` (CSV/TSV)

When you want **structured output** (CSV/TSV) rather than packet listings, use `-T fields` and one or more `-e <field>` options.

## Quick Start
```bash
# CSV with header: time, src IP, dst IP, HTTP host
tshark -r file.pcap -Y http -T fields   -e frame.time -e ip.src -e ip.dst -e http.host   -E header=y -E separator=, -E quote=d
```

## Common Fields to Extract
```text
# Frame / Ethernet
frame.number   frame.time   frame.len
eth.src        eth.dst

# IPv4 / IPv6
ip.src ip.dst ip.proto ip.ttl
ipv6.src ipv6.dst

# TCP / UDP
tcp.srcport tcp.dstport tcp.seq tcp.ack tcp.len tcp.flags tcp.stream
udp.srcport udp.dstport udp.length

# HTTP
http.request.method http.host http.request.uri http.user_agent http.cookie http.response.code

# DNS
dns.qry.name dns.a dns.aaaa dns.ns dns.cname

# TLS
tls.handshake.type tls.handshake.extensions_server_name tls.record.content_type
```

## Formatting Controls (`-E` options for `-T fields`)
```text
-E header=y|n           # print header row
-E separator=/t|/s|<c>  # tab, space, or any char (e.g., , | ;)
-E quote=d|s|n          # double/single/none
-E occurrence=f|l|a     # first, last, or all occurrences of multi-valued fields
-E aggregator=<c>       # joiner for multi-valued fields when occurrence=a (default ,)
-E eol=r|n|rn           # line ending (carriage return, newline, CRLF)
```

### Multi‑valued Fields Example (DNS answers)
```bash
# All A records per response, joined by ';'
tshark -r file.pcap -Y "dns.flags.response==1" -T fields   -e frame.number -e dns.qry.name -e dns.a   -E header=y -E separator=, -E occurrence=a -E aggregator=";"
```

### Using Display Filters but Printing Fields
```bash
# Filter to HTTP requests, print a tidy table
tshark -r file.pcap -Y "http.request" -T fields   -e frame.number -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport   -e http.host -e http.request.method -e http.request.uri   -E header=y -E separator=, -E quote=d
```

### Restrict JSON/PDML Output to Specific Fields
```bash
# Same -e selection works with -T json|ek|pdml to limit output size
tshark -r file.pcap -Y http -T json   -e frame.number -e http.host -e http.request.method
```

### Info Column as a Field
```bash
# Include the classic "Info" column in your export
tshark -r file.pcap -T fields -e frame.number -e _ws.col.info   -E header=y -E separator=/t
```

### Quick Aggregations in the Shell
```bash
# Top HTTP hosts
tshark -r file.pcap -Y http.request -T fields -e http.host  | sort | uniq -c | sort -nr | head

# Count by TCP stream
tshark -r file.pcap -Y tcp -T fields -e tcp.stream  | sort -n | uniq -c | sort -nr | head
```

---

# Statistics & “follow” with `-z`

**Tip:** Use `-q` to suppress per-packet output so only stats print. You can also scope stats with a display filter via `-Y "..."`.

## Protocol Hierarchy (like Wireshark’s *Statistics → Protocol Hierarchy*)
```bash
# Overall protocol breakdown (packets/bytes, per layer)
tshark -r file.pcap -q -z io,phs

# Scoped to one host
tshark -r file.pcap -q -Y "ip.addr==10.0.0.5" -z io,phs
```

## Time‑bucketed I/O stats (`io,stat`)
```bash
# 1‑second buckets: total frames, TCP frames, UDP frames
tshark -r file.pcap -q -z "io,stat,1,COUNT,COUNT(tcp),COUNT(udp)"

# 5‑second buckets: total bytes per bucket and HTTP request count
tshark -r file.pcap -q -z "io,stat,5,SUM(frame.len),COUNT(http.request)"

# SYNs per second (no ACK set) – quick scan detector
tshark -r file.pcap -q -z "io,stat,1,COUNT(tcp.flags.syn==1 && tcp.flags.ack==0)"
```
> Functions include `COUNT(<filter>)`, `SUM(field)`, `MIN(field)`, `MAX(field)`, `AVG(field)`. A plain `COUNT` (without filter) counts **all** frames per bucket.

## Conversations (`conv`)
```bash
# Top talkers by conversation (pairs), frames/bytes both directions
tshark -r file.pcap -q -z conv,ip
tshark -r file.pcap -q -z conv,tcp
tshark -r file.pcap -q -z conv,udp

# Limit to a host, then summarize TCP conversations
tshark -r file.pcap -q -Y "ip.addr==10.0.0.5" -z conv,tcp
```

## Endpoints (per address totals)
```bash
tshark -r file.pcap -q -z endpoints,ip
tshark -r file.pcap -q -z endpoints,ipv6
tshark -r file.pcap -q -z endpoints,eth
```

## Expert Info (errors, warnings, notable events)
```bash
tshark -r file.pcap -q -z expert
```
> Handy for spotting malformed packets, retransmissions (also see `tcp.analysis.*`), protocol errors, etc.

## Follow TCP Stream (reassembled payload)
```bash
# Find stream index for a packet of interest
tshark -r file.pcap -Y "frame.number==123 && tcp" -T fields -e tcp.stream

# ASCII view of reassembled data for that stream
tshark -r file.pcap -q -z "follow,tcp,ascii,7"

# Hex or raw bytes
tshark -r file.pcap -q -z "follow,tcp,hex,7"
tshark -r file.pcap -q -z "follow,tcp,raw,7" > stream7.raw
```
> Tip: Combine with TLS keys to decrypt before following:  
> `tshark -r file.pcap -o tls.keylog_file:/path/keys.log -q -z "follow,tcp,ascii,7"`