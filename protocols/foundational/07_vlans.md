# VLANs (Virtual Local Area Networks)

## Overview
VLANs are logical network segments that group devices together regardless of their physical location. They provide network segmentation, improved security, and better broadcast domain management while operating at Layer 2 of the OSI model.

## Technical Details

### VLAN Characteristics
- Layer: 2 (Data Link)
- IEEE 802.1Q standard
- 12-bit VLAN ID (1-4094)
- Independent broadcast domains
- Logical segmentation
- Cross-switch support

### VLAN Types

1. **Data VLAN**
   - User traffic
   - Department-based
   - Application-based
   - Default VLAN
   - Subnet association

2. **Voice VLAN**
   - Voice traffic
   - QoS priority
   - Auxiliary VLAN
   - CDP/LLDP detection
   - Power over Ethernet

3. **Management VLAN**
   - Network management
   - Device access
   - Secure control plane
   - Out-of-band management
   - SNMP traffic

4. **Native VLAN**
   - Untagged traffic
   - Default VLAN 1
   - Trunk ports
   - Legacy support
   - Security considerations

## Implementation

### Basic Configuration

1. **VLAN Creation**
   ```
   ! Create VLANs
   Switch(config)# vlan 10
   Switch(config-vlan)# name USERS
   Switch(config-vlan)# exit
   
   Switch(config)# vlan 20
   Switch(config-vlan)# name VOICE
   
   ! Configure access ports
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode access
   Switch(config-if)# switchport access vlan 10
   ```

2. **Trunk Configuration**
   ```
   ! Configure trunk port
   Switch(config)# interface GigabitEthernet0/24
   Switch(config-if)# switchport mode trunk
   Switch(config-if)# switchport trunk allowed vlan 10,20
   Switch(config-if)# switchport trunk native vlan 1
   ```

### Advanced Configuration

1. **Voice VLAN**
   ```
   ! Configure voice VLAN
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode access
   Switch(config-if)# switchport access vlan 10
   Switch(config-if)# switchport voice vlan 20
   Switch(config-if)# mls qos trust cos
   ```

2. **Private VLANs**
   ```
   ! Configure private VLANs
   Switch(config)# vlan 100
   Switch(config-vlan)# private-vlan primary
   
   Switch(config)# vlan 101
   Switch(config-vlan)# private-vlan isolated
   
   Switch(config)# vlan 100
   Switch(config-vlan)# private-vlan association 101
   
   ! Configure interfaces
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode private-vlan host
   Switch(config-if)# switchport private-vlan host-association 100 101
   ```

## Design Considerations

### Network Planning
1. **VLAN Design**
   - Number of VLANs
   - VLAN distribution
   - Broadcast domains
   - Subnet mapping
   - Growth planning

2. **Performance Planning**
   - Broadcast control
   - Spanning tree
   - Trunk capacity
   - QoS requirements
   - Resource usage

3. **Security Planning**
   - VLAN isolation
   - Access control
   - Trunk security
   - Management access
   - Monitoring

### High Availability
1. **Redundancy**
   - Trunk links
   - Spanning tree
   - VLAN load balancing
   - Failover paths
   - State synchronization

2. **Recovery**
   - Link failure
   - Switch failure
   - VLAN database
   - Configuration backup
   - Automatic recovery

## Troubleshooting

### Common Issues
1. **Connectivity Problems**
   - VLAN mismatch
   - Trunk issues
   - Native VLAN
   - Port configuration
   - STP blocking

2. **Performance Issues**
   - Broadcast storms
   - Trunk overload
   - Spanning tree
   - Resource exhaustion
   - QoS problems

### Verification Commands
```
show vlan
show vlan brief
show interfaces trunk
show spanning-tree vlan 10
show mac address-table vlan 10
show running-config interface
show interfaces status
```

## Best Practices

### Design Guidelines
1. **VLAN Architecture**
   - Logical grouping
   - Scalable design
   - Clear naming
   - Documentation
   - Change control

2. **Security Implementation**
   - VLAN separation
   - Access control
   - Trunk security
   - Management VLAN
   - Monitoring

### Security Considerations
1. **VLAN Security**
   ```
   ! Disable unused ports
   Switch(config)# interface range GigabitEthernet0/1-24
   Switch(config-if-range)# shutdown
   
   ! Protect trunk ports
   Switch(config)# interface GigabitEthernet0/24
   Switch(config-if)# switchport trunk allowed vlan 10,20
   Switch(config-if)# switchport nonegotiate
   ```

2. **Access Control**
   ```
   ! Configure VACL
   Switch(config)# vlan access-map VLAN10_MAP 10
   Switch(config-access-map)# match ip address 101
   Switch(config-access-map)# action forward
   
   ! Apply to VLAN
   Switch(config)# vlan filter VLAN10_MAP vlan-list 10
   ```

## Interview Tips
- Understand VLAN concepts
- Know VLAN types and uses
- Explain trunking
- Understand security features
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Enterprise networks
  - Data centers
  - Campus networks
  - Voice/data convergence
- Best practices:
  - VLAN design
  - Security implementation
  - Performance tuning
  - Documentation
- Advanced concepts:
  - Private VLANs
  - VLAN trunking
  - QoS integration
  - Layer 3 switching 