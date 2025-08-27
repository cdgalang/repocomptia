# OSI and TCP/IP model

[OSI vs. TCP/IP](https://www.researchgate.net/figure/The-logical-mapping-between-OSI-basic-reference-model-and-the-TCP-IP-stack_fig2_327483011)

Layer 7: Application
-----------------------------------------------------
- Role: Provides services directly to user applications
- Data Unit: Data
- Protocols: HTTP/HTTPS, FTP, SMTP, IMAP, POP3, DNS, SNMP, SSH, Telnet
- Devices: Gateways, Proxies
- Example: Web browser requesting a page from a server

Layer 6: Presentation
-----------------------------------------------------
- Role: Data translation, encryption, compression
- Data Unit: Data
- Protocols: TLS/SSL, JPEG, MPEG, GIF, PNG, ASCII, EBCDIC
- Devices: None dedicated (handled in software)
- Example: Encrypting an HTTPS message, decoding a JPEG image

Layer 5: Session
-----------------------------------------------------
- Role: Establishes, manages, and terminates sessions
- Data Unit: Data
- Protocols: NetBIOS, RPC, SMB, NFS, PPTP
- Devices: None dedicated (handled in OS/software)
- Example: Keeping a video call session alive

Layer 4: Transport
-----------------------------------------------------
- Role: Reliable end-to-end delivery, segmentation/reassembly
- Data Unit: Segments (TCP) / Datagrams (UDP)
- Protocols: TCP, UDP
- Devices: Firewalls (work up to Layer 4), Load Balancers
- Example: TCP 3-way handshake, UDP for video streaming

Layer 3: Network
-----------------------------------------------------
- Role: Logical addressing, routing between networks
- Data Unit: Packets
- Protocols: IP (IPv4/IPv6), ICMP, ARP, RIP, OSPF, EIGRP, BGP
- Devices: Routers, Layer 3 Switches
- Example: Router forwarding packets from 192.168.1.10 to 8.8.8.8

Layer 2: Data Link
-----------------------------------------------------
- Role: Node-to-node delivery, MAC addressing, error detection
- Data Unit: Frames
- Protocols: Ethernet (IEEE 802.3), Wi-Fi (802.11), PPP, ARP
- Devices: Switches, Bridges, NICs
- Example: Switch forwards a frame using MAC address

Layer 1: Physical
-----------------------------------------------------
- Role: Transmission of raw bits (1s and 0s) over physical medium
- Data Unit: Bits
- Protocols: IEEE 802.3 (Ethernet physical), DSL, USB, Bluetooth
- Devices: Hubs, Repeaters, Cables, Connectors
- Example: Electrical signals over Ethernet cable, light pulses in fiber

