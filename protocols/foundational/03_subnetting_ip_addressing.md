# Subnetting & IP Addressing

## Overview
Subnetting is the practice of dividing a network into smaller logical networks. IP addressing is the system used to assign unique addresses to devices on a network.

## Technical Details

### IPv4 Addressing
- 32-bit address space
- Dotted decimal notation (e.g., 192.168.1.1)
- Total addresses: 2^32 (approximately 4.3 billion)

### Address Classes
1. **Class A**
   - Range: 1.0.0.0 to 126.0.0.0
   - Default mask: 255.0.0.0 (/8)
   - First octet: 0xxxxxxx
   - Private range: 10.0.0.0/8

2. **Class B**
   - Range: 128.0.0.0 to 191.255.0.0
   - Default mask: 255.255.0.0 (/16)
   - First octet: 10xxxxxx
   - Private range: 172.16.0.0/12

3. **Class C**
   - Range: 192.0.0.0 to 223.255.255.0
   - Default mask: 255.255.255.0 (/24)
   - First octet: 110xxxxx
   - Private range: 192.168.0.0/16

### Special Addresses
- **Loopback**: 127.0.0.1/8
- **Link-local**: 169.254.0.0/16
- **Multicast**: 224.0.0.0/4
- **Broadcast**: x.x.x.255 (for /24 network)

### Subnet Masks
- Used to define network and host portions
- Common masks:
  - /24 (255.255.255.0)
  - /16 (255.255.0.0)
  - /8 (255.0.0.0)
  - /28 (255.255.255.240)
  - /29 (255.255.255.248)

### CIDR (Classless Inter-Domain Routing)
- Notation: IP address/prefix length
- Examples:
  - 192.168.1.0/24
  - 10.0.0.0/8
  - 172.16.0.0/12

### Subnetting Calculations
1. **Network Address**: First address in subnet
2. **Broadcast Address**: Last address in subnet
3. **First Usable**: Network address + 1
4. **Last Usable**: Broadcast address - 1
5. **Number of Hosts**: 2^(32-prefix) - 2

### Quick Reference
- /24 = 256 addresses (254 usable)
- /25 = 128 addresses (126 usable)
- /26 = 64 addresses (62 usable)
- /27 = 32 addresses (30 usable)
- /28 = 16 addresses (14 usable)
- /29 = 8 addresses (6 usable)
- /30 = 4 addresses (2 usable)
- /31 = 2 addresses (point-to-point)
- /32 = 1 address (single host)

## IPv6 Addressing
- 128-bit address space
- Eight 16-bit hexadecimal blocks
- Example: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
- Shorthand rules:
  - Leading zeros in a block can be omitted
  - Consecutive zero blocks can be replaced with ::

### IPv6 Address Types
1. **Global Unicast**
   - Start with 2000::/3
   - Publicly routable

2. **Link-Local**
   - Start with fe80::/10
   - Not routable

3. **Unique Local**
   - Start with fc00::/7
   - Private addressing

## Troubleshooting Tools
1. **IP Configuration**
   - ipconfig/ifconfig
   - ip addr
   - netsh

2. **Subnet Calculators**
   - ipcalc
   - Online calculators
   - Subnet masks charts

3. **Connectivity Testing**
   - ping
   - traceroute
   - nslookup

## Interview Tips
- Know how to calculate subnets quickly
- Understand VLSM (Variable Length Subnet Masking)
- Be able to identify network/broadcast addresses
- Understand private vs public addressing
- Know common subnet masks and their uses
- Be familiar with IPv6 basics
- Understand NAT and its relationship to private addressing
- Know how to troubleshoot IP addressing issues 