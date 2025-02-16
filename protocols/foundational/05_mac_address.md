# MAC Address (Media Access Control)

## Overview
A MAC address is a unique identifier assigned to network interfaces for communications at the data link layer. It's a 48-bit (6-byte) number typically represented in hexadecimal format.

## Technical Details

### Address Format
- 48 bits (6 bytes) long
- Written as 12 hexadecimal digits
- Format: MM:MM:MM:SS:SS:SS
  - First 6 digits (MM:MM:MM): Manufacturer ID (OUI)
  - Last 6 digits (SS:SS:SS): Serial Number

### Address Types
1. **Unicast**
   - Individual device address
   - Least significant bit of first byte = 0
   - Example: 00:1A:2B:3C:4D:5E

2. **Multicast**
   - Group address
   - Least significant bit of first byte = 1
   - Example: 01:00:5E:00:00:01

3. **Broadcast**
   - All devices on network
   - All bits set to 1
   - FF:FF:FF:FF:FF:FF

### Special MAC Addresses
- **Locally Administered**: Second least significant bit of first byte = 1
- **Universally Administered**: Second least significant bit of first byte = 0
- **Virtual MAC**: Used in virtualization
- **Burned-in Address (BIA)**: Factory-assigned permanent address

## MAC Address Operations

### Address Resolution
1. **ARP (IPv4)**
   - Maps IP addresses to MAC addresses
   - Broadcast query
   - Cached results

2. **NDP (IPv6)**
   - Neighbor Discovery Protocol
   - ICMPv6-based
   - More efficient than ARP

### Frame Processing
1. **Source MAC**
   - Inserted by sending device
   - Used for return traffic

2. **Destination MAC**
   - Determined through ARP/NDP
   - Used for frame forwarding

### MAC Table Operations
1. **Learning**
   - Switch learns MAC-to-port mapping
   - Source address examination
   - Dynamic updates

2. **Aging**
   - Entries timeout
   - Typically 300 seconds
   - Configurable

3. **Forwarding**
   - Based on destination MAC
   - Unknown unicast flooding
   - CAM table lookup

## Security Considerations

### MAC Spoofing
- Changing MAC address
- Detection methods
- Prevention techniques:
  - Port security
  - MAC filtering
  - 802.1X

### MAC Filtering
1. **Static**
   - Manually configured
   - Limited scalability
   - High maintenance

2. **Dynamic**
   - Learning-based
   - More flexible
   - Better scalability

### Port Security
1. **Sticky Learning**
   - Dynamic to static conversion
   - Port-based
   - Violation actions

2. **MAC Limits**
   - Maximum addresses per port
   - Overflow handling
   - Security actions

## Troubleshooting

### Common Issues
1. **Duplicate MAC Addresses**
   - Virtual machine conflicts
   - Misconfigured devices
   - Network loops

2. **MAC Flapping**
   - Address appears on multiple ports
   - Usually indicates loop
   - STP-related issues

3. **Table Overflow**
   - CAM table full
   - Flooding behavior
   - Performance impact

### Tools and Commands
1. **Windows**
   - `ipconfig /all`
   - `getmac`
   - Network adapter properties

2. **Linux/Unix**
   - `ifconfig`
   - `ip link show`
   - `macchanger`

3. **Network Tools**
   - Wireshark
   - tcpdump
   - arp-scan

## Interview Tips
- Understand MAC address structure
- Know difference between MAC and IP
- Explain address resolution process
- Understand security implications
- Be familiar with common issues
- Know troubleshooting approaches
- Understand:
  - MAC table operations
  - Address types
  - Security features
  - Virtualization impacts
- Real-world examples:
  - Network segmentation
  - Security implementation
  - Troubleshooting scenarios 