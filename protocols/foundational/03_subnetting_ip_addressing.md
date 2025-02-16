# Subnetting & IP Addressing

## Overview
Subnetting is the practice of dividing an IP network into sub-networks to improve network security, performance, and address utilization. IP addressing is the foundation of network communication, providing unique identifiers for devices in a network.

## Technical Details

### IPv4 Addressing
- 32-bit address space
- Dotted decimal notation (e.g., 192.168.1.1)
- Network and host portions
- Subnet mask notation
- CIDR notation

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
- Network address (all host bits 0)
- Broadcast address (all host bits 1)
- Loopback: 127.0.0.0/8
- Link-local: 169.254.0.0/16
- Multicast: 224.0.0.0/4

## Implementation

### Subnetting Process

1. **Network Requirements**
   - Number of subnets needed
   - Hosts per subnet
   - Network address space
   - Growth considerations
   - Security requirements

2. **Subnet Calculation**
   ```
   ! Example: Subnetting 192.168.1.0/24 into 4 subnets
   Subnet 0: 192.168.1.0/26   (1-62 hosts)
   Subnet 1: 192.168.1.64/26  (65-126 hosts)
   Subnet 2: 192.168.1.128/26 (129-190 hosts)
   Subnet 3: 192.168.1.192/26 (193-254 hosts)
   ```

3. **VLSM (Variable Length Subnet Masking)**
   ```
   ! Example: VLSM allocation
   Network: 172.16.0.0/16
   
   Subnet A: 172.16.0.0/20   (4094 hosts)
   Subnet B: 172.16.16.0/22  (1022 hosts)
   Subnet C: 172.16.20.0/24  (254 hosts)
   Subnet D: 172.16.21.0/26  (62 hosts)
   ```

### Address Planning

1. **Hierarchical Design**
   ```
   ! Example: Enterprise addressing
   Corporate: 10.0.0.0/8
   Region 1: 10.1.0.0/16
   Region 2: 10.2.0.0/16
   
   Site 1: 10.1.1.0/24
   Site 2: 10.1.2.0/24
   ```

2. **Address Assignment**
   ```
   ! Static assignment
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip address 192.168.1.1 255.255.255.0
   
   ! Dynamic assignment (DHCP)
   Router(config)# ip dhcp pool LAN1
   Router(dhcp-config)# network 192.168.1.0 255.255.255.0
   Router(dhcp-config)# default-router 192.168.1.1
   ```

## Design Considerations

### Network Planning
1. **Address Space Selection**
   - Private vs public addressing
   - Growth requirements
   - Subnet size planning
   - Address conservation
   - Summarization opportunities

2. **Subnet Design**
   - Department needs
   - Geographic distribution
   - Security zones
   - Network services
   - Future expansion

3. **Documentation**
   - IP address inventory
   - Subnet allocation
   - DHCP scopes
   - Network diagrams
   - Change management

### Performance Impact
1. **Routing Efficiency**
   - Route summarization
   - VLSM implementation
   - Subnet boundaries
   - Default routes
   - Network size

2. **Address Management**
   - IPAM solutions
   - DHCP services
   - DNS integration
   - Monitoring tools
   - Automation

## Troubleshooting

### Common Issues
1. **Addressing Problems**
   - IP conflicts
   - Incorrect subnet masks
   - DHCP issues
   - Routing problems
   - NAT failures

2. **Subnet Issues**
   - Overlapping subnets
   - Insufficient addresses
   - Broadcast storms
   - Poor summarization
   - Fragmented space

### Verification Commands
```
show ip interface brief
show ip route
show ip dhcp binding
ping
traceroute
show ip protocols
show ip arp
```

## Best Practices

### Design Guidelines
1. **Address Planning**
   - Use VLSM where appropriate
   - Plan for growth
   - Document thoroughly
   - Implement IPAM
   - Regular audits

2. **Subnet Organization**
   - Logical grouping
   - Clear boundaries
   - Efficient utilization
   - Security zones
   - Easy management

### Security Considerations
1. **Network Segmentation**
   ```
   ! Configure VLANs with subnets
   Router(config)# interface vlan 10
   Router(config-if)# ip address 192.168.10.1 255.255.255.0
   
   ! Configure ACLs
   Router(config)# access-list 101 permit ip 192.168.10.0 0.0.0.255 any
   ```

2. **Address Space Protection**
   ```
   ! Configure DHCP snooping
   Switch(config)# ip dhcp snooping
   Switch(config)# ip dhcp snooping vlan 10
   
   ! Configure ARP inspection
   Switch(config)# ip arp inspection vlan 10
   ```

## Interview Tips
- Understand subnetting math
- Know address classes and ranges
- Calculate subnet sizes quickly
- Understand VLSM concepts
- Be familiar with:
  - Address planning
  - Subnet design
  - CIDR notation
  - Special addresses
- Real-world scenarios:
  - Enterprise networks
  - Branch offices
  - Data centers
  - Cloud migrations
- Best practices:
  - Documentation
  - Address management
  - Security implementation
  - Growth planning
- Advanced concepts:
  - Route summarization
  - Address conservation
  - NAT/PAT
  - IPv6 transition

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