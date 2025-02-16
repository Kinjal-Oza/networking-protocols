# DHCP (Dynamic Host Configuration Protocol)

## Overview
DHCP is a network protocol that automatically assigns IP addresses and other network configuration parameters to devices on a network. It simplifies network administration by centralizing and automating IP address management.

## Technical Details

### Protocol Characteristics
- Application Layer Protocol
- UDP Ports: 67 (Server), 68 (Client)
- Supports IPv4 and IPv6 (DHCPv6)
- Lease-based address assignment
- Client-server architecture
- Broadcast/Multicast-based discovery

### DHCP Process (DORA)

1. **Discovery**
   - Client broadcasts DHCPDISCOVER
   - Source: 0.0.0.0
   - Destination: 255.255.255.255
   - Contains client MAC address

2. **Offer**
   - Server responds with DHCPOFFER
   - Proposed IP address
   - Lease duration
   - Additional parameters

3. **Request**
   - Client broadcasts DHCPREQUEST
   - Accepting offered IP
   - Can request specific parameters
   - Confirms selected server

4. **Acknowledgment**
   - Server sends DHCPACK
   - Confirms IP assignment
   - Provides all parameters
   - Starts lease timer

### DHCP Options
1. **Common Options**
   - Subnet Mask (1)
   - Router/Gateway (3)
   - DNS Servers (6)
   - Domain Name (15)
   - Lease Time (51)
   - DHCP Server ID (54)
   - Renewal Time (58)
   - Rebinding Time (59)

2. **Additional Options**
   - Time Servers (4)
   - NTP Servers (42)
   - WINS Servers (44)
   - TFTP Server (66)
   - Boot Filename (67)
   - Custom Options (128-254)

## Implementation

### Basic Configuration

1. **DHCP Server (Cisco IOS)**
   ```
   ! Configure DHCP pool
   Router(config)# ip dhcp pool POOL1
   Router(dhcp-config)# network 192.168.1.0 255.255.255.0
   Router(dhcp-config)# default-router 192.168.1.1
   Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
   Router(dhcp-config)# lease 7
   
   ! Exclude addresses
   Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
   ```

2. **DHCP Relay**
   ```
   ! Configure helper address
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip helper-address 10.0.0.1
   ```

### Advanced Configuration

1. **DHCPv6 Configuration**
   ```
   ! Enable IPv6 routing
   Router(config)# ipv6 unicast-routing
   
   ! Configure DHCPv6 pool
   Router(config)# ipv6 dhcp pool IPV6-POOL
   Router(config-dhcpv6)# address prefix 2001:db8::/64
   Router(config-dhcpv6)# dns-server 2001:4860:4860::8888
   ```

2. **DHCP Options**
   ```
   ! Configure custom options
   Router(config)# ip dhcp pool POOL1
   Router(dhcp-config)# option 42 ip 192.168.1.100
   Router(dhcp-config)# option 150 ip 192.168.1.200
   ```

## Design Considerations

### Network Planning
1. **Address Space**
   - Subnet sizing
   - Lease duration
   - Reserved ranges
   - Growth planning

2. **Redundancy**
   - Multiple DHCP servers
   - Failover configuration
   - Split scope design
   - High availability

3. **Scalability**
   - Pool capacity
   - Lease times
   - Network segments
   - DHCP relay

### High Availability
1. **Server Redundancy**
   - Primary/Secondary servers
   - Load balancing
   - Failover configuration
   - Database synchronization

2. **Lease Management**
   - Optimal lease times
   - Reservation policies
   - Address recovery
   - Conflict detection

## Troubleshooting

### Common Issues
1. **Client Problems**
   - No IP address
   - Wrong IP range
   - Lease renewal failure
   - Option misconfiguration

2. **Server Problems**
   - Pool exhaustion
   - Conflicting addresses
   - Relay issues
   - Database corruption

3. **Network Issues**
   - Broadcast blocking
   - VLAN configuration
   - Helper address
   - Relay problems

### Verification Commands
```
show ip dhcp binding
show ip dhcp pool
show ip dhcp server statistics
debug ip dhcp server events
show ip dhcp conflict
show ip helper-address
```

## Best Practices

### Design Guidelines
1. **Pool Configuration**
   - Appropriate scope size
   - Reserved addresses
   - Option configuration
   - Lease duration

2. **Security Measures**
   - DHCP snooping
   - Option 82
   - Access control
   - Authentication

3. **Management**
   - Regular monitoring
   - Lease tracking
   - Conflict resolution
   - Documentation

### Operational Procedures
1. **Maintenance**
   - Database backup
   - Log monitoring
   - Performance tuning
   - Regular updates

2. **Documentation**
   - Pool configuration
   - Reserved addresses
   - Option settings
   - Network topology

## Security Considerations

### DHCP Security
1. **DHCP Snooping**
   ```
   ! Enable DHCP snooping
   Switch(config)# ip dhcp snooping
   Switch(config)# ip dhcp snooping vlan 10
   
   ! Configure trusted port
   Switch(config-if)# ip dhcp snooping trust
   ```

2. **Option 82**
   ```
   ! Enable option 82
   Switch(config)# ip dhcp snooping information option
   ```

## Interview Tips
- Understand DHCP operation (DORA)
- Know common DHCP options
- Explain lease process
- Understand security features
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security measures
- Real-world scenarios:
  - Enterprise networks
  - Service provider networks
  - Branch deployment
- Best practices:
  - Pool design
  - Lease management
  - Security implementation
  - High availability
- Advanced concepts:
  - DHCPv6
  - DHCP failover
  - Option configuration
  - Relay operation 