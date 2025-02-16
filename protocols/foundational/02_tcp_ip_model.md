# TCP/IP Model (Internet Protocol Suite)

## Overview
The TCP/IP Model is the foundational protocol suite of the Internet, consisting of four layers. It's more practical and implementation-focused compared to the OSI Model.

## Technical Details

### Layer Structure

1. **Network Access Layer (Layer 1)**
   - Combines OSI Physical and Data Link layers
   - Hardware addressing
   - Media access control
   - Examples: Ethernet, WiFi, PPP
   - Functions:
     - Physical transmission
     - Frame formatting
     - Error detection
     - MAC addressing

2. **Internet Layer (Layer 2)**
   - Corresponds to OSI Network Layer
   - Key Protocols:
     - IPv4 (RFC 791)
     - IPv6 (RFC 2460)
     - ICMP
     - ARP
   - Functions:
     - Logical addressing
     - Routing
     - Packet fragmentation
     - Reassembly

3. **Transport Layer (Layer 3)**
   - End-to-end communication
   - Key Protocols:
     - TCP (Transmission Control Protocol)
       - Ports: 0-65535
       - Common ports: 20/21 (FTP), 22 (SSH), 80 (HTTP), 443 (HTTPS)
     - UDP (User Datagram Protocol)
       - Ports: 0-65535
       - Common ports: 53 (DNS), 67/68 (DHCP)
   - Functions:
     - Segmentation
     - Flow control
     - Error recovery
     - Connection management

4. **Application Layer (Layer 4)**
   - Combines OSI Session, Presentation, and Application layers
   - Common Protocols:
     - HTTP/HTTPS (80/443)
     - FTP (20/21)
     - SMTP (25)
     - DNS (53)
     - SSH (22)
     - Telnet (23)

### TCP Three-Way Handshake
1. SYN (Client → Server)
2. SYN-ACK (Server → Client)
3. ACK (Client → Server)

### TCP vs UDP
- **TCP**
  - Connection-oriented
  - Reliable delivery
  - Flow control
  - Error recovery
  - Used for: Web browsing, email, file transfer

- **UDP**
  - Connectionless
  - Best-effort delivery
  - No flow control
  - No error recovery
  - Used for: Streaming, DNS queries, online gaming

## Key Concepts

### IP Addressing
- IPv4: 32-bit addresses (e.g., 192.168.1.1)
- IPv6: 128-bit addresses (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334)
- Subnetting
- CIDR notation

### Port Numbers
- Well-known ports: 0-1023
- Registered ports: 1024-49151
- Dynamic ports: 49152-65535

## Troubleshooting Tools
1. **Network Layer**
   - ping
   - traceroute/tracert
   - ipconfig/ifconfig
   - route

2. **Transport Layer**
   - netstat
   - tcpdump
   - Wireshark
   - port scanners

3. **Application Layer**
   - nslookup
   - dig
   - telnet
   - curl

## Interview Tips
- Understand differences between TCP/IP and OSI models
- Know common protocols and their port numbers
- Explain TCP 3-way handshake
- Understand IP addressing and subnetting
- Be familiar with common troubleshooting tools
- Know when to use TCP vs UDP
- Understand network security implications at each layer 