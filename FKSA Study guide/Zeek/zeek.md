# Zeek Offline PCAP Cheat Sheet

> Your quick-reference for analyzing `.pcap` files with Zeek — options, filters, and must‑know commands.  
> Keep this in your repo or notes. Safe to run fully **offline**.

---

## 📦 Quick Start (one-liners)

```bash
# Basic: parse a PCAP (writes logs to current dir)
zeek -r traffic.pcap

# Ignore bad checksums (common with off-box captures)
zeek -Cr traffic.pcap

# Write logs into a dedicated folder
mkdir -p out && (cd out && zeek -Cr ../traffic.pcap)

# Run with minimal scripts (great for debugging script errors)
zeek -b -Cr traffic.pcap

# Add a BPF (capture) filter while reading a pcap (see BPF section below)
zeek -Cr traffic.pcap tcp port 443

# Emit JSON logs instead of TSV (line-delimited JSON)
zeek -Cr traffic.pcap policy/tuning/json-logs.zeek
```

---

## 🗂️ Core Logs You’ll See Often

- `conn.log` – one line per flow (4‑tuple, proto, service, bytes, state)
- `dns.log` – query/response, rcode, types
- `http.log` – host, uri, method, status, user‑agent
- `ssl.log` / `tls.log` – TLS version, ciphers, SNI
- `x509.log` – certificate subjects/issuers/validity
- `files.log` – file transfers, MIME, hashes, extracted path
- `weird.log` – anomalies in parsing/protocols
- `notice.log` – policy-driven alerts (e.g., scans when enabled)

> Tip: Zeek logs are **tab-separated**. Use `zeek-cut` for clean field extraction.

---

## 🧭 Fields to Remember (esp. in `conn.log`)

- `ts` – epoch timestamp (float) when the event/flow started
- `uid` – unique id (join key across logs)
- `id.orig_h`, `id.orig_p` – originator (client) IP/port
- `id.resp_h`, `id.resp_p` – responder (server) IP/port
- `proto` – `tcp` / `udp` / `icmp`
- `service` – inferred app (e.g., `http`, `dns`, `ssh`), or `-`
- `duration` – seconds the flow lasted
- `orig_bytes` / `resp_bytes` – bytes each side sent
- `conn_state` – how it ended; common: `SF` (ok), `S0` (no reply), `REJ` (rejected)

---

## 🔎 Reading Logs Nicely (alignment & headers)

```bash
# Show a tidy table with header, human time, aligned columns
zeek-cut -c -d ts id.orig_h id.resp_h proto service id.resp_p < conn.log | column -t -s $'\t'

# Follow a log live (if you’re running Zeek continuously elsewhere)
tail -F dns.log | zeek-cut -d ts id.orig_h query rcode_name | column -t -s $'\t'
```

**Plain tools (no zeek-*):**
```bash
# Include the #fields header (TSV) and align using only POSIX tools
awk 'BEGIN{FS=OFS="\t"} /^#fields/ {h=$0; sub(/^#fields[ \t]+/,"",h); next} /^[^#]/ { if(!p){print h; p=1} print }' conn.log | column -t -s $'\t'
```

**Wide logs in a pager:**
```bash
less -S random.log   # ← requested command (turns off line wrapping in less)
```

---

## 🧪 Must-Know One-Liners (memorizable)

### Traffic overview
```bash
# Who talks to whom, with proto/service (first 10)
cat conn.log | zeek-cut -d ts id.orig_h id.resp_h proto service | head

# Top source IPs (talkers)
cat conn.log | zeek-cut id.orig_h | sort | uniq -c | sort -nr | head

# Top destination ports
cat conn.log | zeek-cut id.resp_p | sort | uniq -c | sort -nr | head

# Longest connections
cat conn.log | zeek-cut duration id.orig_h id.resp_h id.resp_p service | sort -nr | head
```

### DNS triage
```bash
# Recent NXDOMAIN rate
total=$(tail -n 1000 dns.log | wc -l); nxd=$(tail -n 1000 dns.log | zeek-cut rcode_name | grep -c NXDOMAIN || true); echo "$nxd / $total NXDOMAIN"

# Suspicious long queries (exfil hints)
cat dns.log | zeek-cut query | awk 'length($0)>60' | head
```

### Scan-ish signals
```bash
# Connection states distribution
cat conn.log | zeek-cut conn_state | sort | uniq -c | sort -nr | head

# Lots of distinct dst ports per src (port scan hint)
cat conn.log | zeek-cut id.orig_h id.resp_p | sort -u | awk '{c[$1]++} END{for (h in c) print c[h], h}' | sort -nr | head
```

### HTTP quick view
```bash
# Who hit what URL
cat http.log | zeek-cut id.orig_h host uri method status_code user_agent | head

# 4xx/5xx only
cat http.log | zeek-cut status_code id.orig_h host uri | awk '$1 ~ /^4|5/'
```

### TLS/x509 sanity
```bash
# Self-signed (issuer == subject)
cat x509.log | zeek-cut certificate.subject certificate.issuer | awk '$1==$2' | head

# Validity window (spot expired/not-yet-valid)
cat x509.log | zeek-cut validity.not_valid_before validity.not_valid_after | head
```

---

## 🧰 Offline Options & Flags (you’ll actually use)

- `-r <pcap>` – read from capture file
- `-C` – ignore invalid (bad) checksums
- `-b` – “bare” mode; load minimal scripts (debug/benchmark)
- `-e '<script>'` – inline Zeek code (great for quick tests)
- (BPF filter) – add at end of command (see below)

**Info/diagnostics:**
- `-v` – version
- `-N` – list plugins/features

> Note: You can set the output directory inline:  
> `zeek -Cr traffic.pcap -e 'redef Log::default_log_path="out";'`

---

## 🔐 BPF (Capture) Filter Examples (work with `-r` too)

Append any of these to your Zeek command (after the pcap/flags):

```bash
# Only HTTPS
tcp port 443

# HTTP or HTTPS
tcp and \(port 80 or port 443\)

# Only DNS
udp port 53

# Single host or subnet
host 10.0.0.5
net 192.168.0.0/16

# Exclude noisy protocols
not arp and not icmp
```

**Examples:**
```bash
zeek -Cr traffic.pcap tcp port 443
zeek -Cr traffic.pcap host 10.0.0.5 and udp port 53
```

---

## 📦 File Extraction & Hashing (offline malware hunting)

```bash
# Extract all files Zeek observes in the PCAP
rm -rf extracted && mkdir extracted
zeek -Cr traffic.pcap policy/files/extract-all-files.zeek

# See what came out, get hashes
ls -l extracted
find extracted -maxdepth 1 -type f -print0 | xargs -0 sha256sum > hashes.txt
```

Tie files to HTTP context:
```bash
cat files.log | zeek-cut fuid tx_hosts rx_hosts source mime_type extracted | head
cat http.log  | zeek-cut uid id.orig_h host uri referrer user_agent | head
```

---

## 🧯 JSON Output (for SIEM/scripts)

```bash
# One-off
zeek -Cr traffic.pcap policy/tuning/json-logs.zeek

# Make it the default for zeekctl deployments (site-wide)
echo '@load policy/tuning/json-logs' | sudo tee -a $(zeek-config --site_dir)/local.zeek
zeekctl deploy
```

Optional (in `local.zeek`):
```zeek
redef LogAscii::json_timestamps = JSON::TS_ISO8601; # or JSON::TS_MILLIS / JSON::TS_EPOCH
redef LogAscii::json_outbound_escaped = T;
```

---

## 🧷 Slicing by Row/Time

```bash
# Absolute file lines 50–60 (incl. headers & comments)
sed -n '50,60p' conn.log

# Data rows 50–60 (skip Zeek # headers)
awk '!/^#/ { if (++n>=50 && n<=60) print }' conn.log

# Time window (epoch seconds)
start=1724500000; end=1724503600
cat conn.log | zeek-cut ts id.orig_h id.resp_h service | awk -v s=$start -v e=$end '$1>=s && $1<=e'
```

---

## 🧳 Working with Rotated Logs

```bash
# Read a gzipped conn log
zcat logs/2025-08-25/conn.log.gz | zeek-cut id.orig_h id.resp_h | head

# Search across days
zgrep -h 'NXDOMAIN' logs/*/dns.log.gz | wc -l
```

---

## ✅ Safety & VM Tips (offline workflows)

- Take a VM **snapshot** first; keep VM **offline** while analyzing.
- **Update** tools (parser bugs are rare but possible).
- Treat extracted files like hazardous samples; move **hashes, not binaries**.
- Disable drag‑and‑drop/shared folders unless you need them.

---

## 🧠 Mental Model

- Zeek is *not* an inline blocker; it’s a **NSM/analysis engine**.
- Start with `conn.log` → pivot to `dns.log` / `http.log` / `tls.log` / `files.log`.
- Use `uid` to join records across logs.
- Summaries first, details second.

---

Happy hunting! 🕵️‍♂️📦✨
