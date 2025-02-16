# MAC Address (Media Access Control)

## Overview
MAC addresses are unique 48-bit hardware identifiers assigned to network interfaces. They operate at Layer 2 of the OSI model and are essential for frame delivery in local area networks.

## Technical Details

### Address Structure
- 48-bit length (6 bytes)
- Hexadecimal notation (e.g., 00:1A:2B:3C:4D:5E)
- First 24 bits: OUI (Organizationally Unique Identifier)
- Last 24 bits: NIC specific
- Broadcast: FF:FF:FF:FF:FF:FF
- Multicast: First bit set to 1

### Address Types

1. **Unicast**
   - Individual device addressing
   - Unique per interface
   - Point-to-point communication
   - Frame forwarding
   - ARP resolution

2. **Multicast**
   - Group addressing
   - First bit set
   - Group communication
   - IGMP snooping
   - MLD support

3. **Broadcast**
   - Network-wide communication
   - All bits set
   - ARP requests
   - DHCP discovery
   - Network flooding

### MAC Operations

1. **Address Learning**
   - Source MAC learning
   - CAM table population
   - Aging timers
   - Table limits
   - Flooding behavior

2. **Frame Forwarding**
   - Destination lookup
   - Port association
   - Unknown unicast
   - Broadcast handling
   - Loop prevention

## Implementation

### Basic Configuration

1. **Port Security**
   ```
   ! Configure port security
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode access
   Switch(config-if)# switchport port-security
   Switch(config-if)# switchport port-security maximum 2
   Switch(config-if)# switchport port-security violation shutdown
   ```

2. **MAC Address Table**
   ```
   ! Configure MAC address table
   Switch(config)# mac address-table aging-time 300
   
   ! Configure static MAC
   Switch(config)# mac address-table static 0000.1111.2222 vlan 10 interface GigabitEthernet0/1
   ```

### Advanced Configuration

1. **MAC-based VLAN Assignment**
   ```
   ! Configure VLAN groups
   Switch(config)# vlan group Engineers mac-addresses
   Switch(config-vlan-group)# mac-address 0000.1111.2222
   Switch(config-vlan-group)# vlan 10
   
   ! Configure interface
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode access
   Switch(config-if)# switchport access vlan dynamic
   ```

2. **MAC Authentication**
   ```
   ! Configure 802.1X
   Switch(config)# aaa new-model
   Switch(config)# aaa authentication dot1x default group radius
   
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# authentication port-control auto
   Switch(config-if)# dot1x pae authenticator
   ```

## Design Considerations

### Network Planning
1. **Table Size**
   - CAM table capacity
   - Aging timers
   - Learning limits
   - Overflow handling
   - Performance impact

2. **Security Planning**
   - Port security
   - MAC filtering
   - Authentication
   - Monitoring
   - Logging

3. **Performance Planning**
   - Broadcast control
   - Unknown unicast
   - Table optimization
   - Learning rate
   - Resource usage

### High Availability
1. **Redundancy**
   - Duplicate MAC detection
   - HSRP/VRRP
   - Spanning tree
   - Link aggregation
   - NIC teaming

2. **Recovery**
   - Table backup
   - Fast convergence
   - Error handling
   - Failure detection
   - Automatic recovery

## Troubleshooting

### Common Issues
1. **MAC Conflicts**
   - Duplicate addresses
   - Spoofing attempts
   - Virtual machine issues
   - NIC failures
   - Configuration errors

2. **Table Problems**
   - Table overflow
   - Incorrect learning
   - Aging issues
   - Memory exhaustion
   - Performance degradation

### Verification Commands
```
show mac address-table
show mac address-table count
show mac address-table aging-time
show port-security
show interfaces status
show spanning-tree
show vlan
```

## Best Practices

### Design Guidelines
1. **MAC Management**
   - Regular table maintenance
   - Aging timer optimization
   - Security implementation
   - Monitoring setup
   - Documentation

2. **Security Implementation**
   - Port security
   - MAC authentication
   - Storm control
   - DHCP snooping
   - ARP inspection

### Security Considerations
1. **Port Protection**
   ```
   ! Configure storm control
   Switch(config-if)# storm-control broadcast level 20
   Switch(config-if)# storm-control multicast level 30
   Switch(config-if)# storm-control unicast level 40
   
   ! Configure DHCP snooping
   Switch(config)# ip dhcp snooping
   Switch(config)# ip dhcp snooping vlan 10
   ```

2. **MAC Security**
   ```
   ! Configure MAC ACL
   Switch(config)# mac access-list extended BLOCK_MAC
   Switch(config-ext-macl)# deny host 0000.1111.2222 any
   Switch(config-ext-macl)# permit any any
   
   ! Apply to interface
   Switch(config-if)# mac access-group BLOCK_MAC in
   ```

## Interview Tips
- Understand MAC address structure
- Know address types and purposes
- Explain learning process
- Understand security features
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Security implementations
  - Performance impacts
- Real-world scenarios:
  - Network security
  - Virtual environments
  - Data center networks
  - Campus networks
- Best practices:
  - Table management
  - Security configuration
  - Performance tuning
  - Monitoring setup
- Advanced concepts:
  - MAC mobility
  - Virtual MAC addresses
  - Provider MAC addresses
  - MAC authentication 