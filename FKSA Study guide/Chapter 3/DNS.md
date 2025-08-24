# DNS

- domain name server, tranlates IP addresses into human readable format, and vice versa.
- hostname (www.example.com) converts into IP address (192.168.x.x for example)

## Loading a webpage

- DNS Recursor
    - designed to receive queries from client
- Root Nameserver
    - translating human readable host into IP address
- TLD nameserver
    - Top Level Domain, next step for a specific IP address and its hosts the last portion of a host name. (.com, for example)
- Authoritative nameserver
    - last stop in the nameserver, it will return the IP address for the requested hostname back  to the DNS recursor.

# DNS records

- A record
    - maps a domain name to IPv4
- AAAA record 
    - maps a domain name to IPv6
- CNAME record
    - canonical name
    - an alias for another domain name
- MX record
    - tells email servers where to deliver mail for domain
- TXT record
    - stores information
- NS record
    - Nameserver
    - lists the authoritative DNS servers for a domain
- SOA record
    - Start of Authority
    - contains important info about a zone
        - primary dns server
        - contact email
        - serial number
        - refresh/retry times
- PTR record
    - Pointer record
    - maps IP address to domain name (reverse lookup)
- SRV record
    - Service record
    - defines the location (host/port) of specific services


