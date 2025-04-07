# TCP/IP and OSI Models
---

[TCP/IP and OSI model comparison](https://github.com/cdgalang/repocomptia/blob/main/Chapter%202/tcpiposi.jpg)

**TCP/IP Model**

* Network Access Layer
    * deals with physical transmission of data
    * how is data is sent over specific hardware such as Ethernet and Wi-Fi

* Internet Layer
    * deals with addressing and routing data packets across networks
    * IP is used to provide addressing and routing information

* Transport Layer
    * ensures reliable delivery between applications
    * uses TCP and UDP

* Application Layer
    * application-specific protocols

**OSI Model**

* Physical Layer
    * physical connection between devices 

* Data Link Layer
    * reliable transfer of data between two directly connected nodes

* Network Layer
    * TCP/IP Internet Layer
    * handles routing and addressing

* Transport Layer
    * complete data transfer
    * error recovery, flow control, and possibly reordering of data packets

* Session Layer
    * Establishes, manages and terminates connections between applications

* Presentation Layer
    * data formatting, encryption, and compression

* Application Layer
    * application-specific protocols operate

*Things to consider*
* Firewalls typically operate at Network and Transport layer for filtering trafic based on IP addresses and port numbers
* Encryption protocols like SSL/TLS work at Transport Layer (TCP/IP Model) or presentation layer (OSI Model)
* Antivirus software operates at the Application Layer
