# IDS and IPS, Snort and Suricata

## Intrusion Detection System
- watches network traffic and alerts if there is malicious activity
- snort uses rule-based language that combines anomaly, protocol and signature inspection methods to detect potentially malicious activity
- can spot DDOS, DOS, and other attacks
- signature based detection
    - looks for known patterns of attacks
- anomaly based detection
    - if sees if there is an unusual traffic
- stateful protocol
    - rule based
        - using predefined rules made by admins
        - if it breaks rules, it will alert admin
        

## Intrusion Prevention System
- watches network traffic and blocks/acts on threats in real time
- 