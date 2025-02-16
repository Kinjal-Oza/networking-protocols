# DTP (Dynamic Trunking Protocol)

## Overview
DTP is a Cisco proprietary protocol that enables network switches to automatically negotiate trunking on a link between two devices. It manages the switch-to-switch links by automatically configuring trunk and access ports, reducing manual configuration requirements.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Cisco Proprietary
- Multicast MAC: 01:00:0C:CC:CC:CC
- Protocol Type: 0x2004
- Default Timer: 30 seconds
- Negotiation enabled by default

### DTP Modes

1. **Auto**
   - Passive negotiation
   - Can become trunk
   - Default for most ports
   - Listens for DTP packets

2. **Desirable**
   - Active negotiation
   - Initiates trunking
   - Sends DTP packets
   - Becomes trunk if agreed

3. **Trunk**
   - Forces trunking
   - Always trunks
   - Sends DTP packets
   - No negotiation needed

4. **Access**
   - Forces access mode
   - Never trunks
   - No DTP packets
   - No negotiation

5. **Nonegotiate**
   - Disables DTP
   - Manual configuration
   - No DTP packets
   - Fixed mode

### Mode Combinations
| Local Port | Remote Port | Result      |
|------------|-------------|-------------|
| Auto       | Auto        | Access      |
| Auto       | Desirable   | Trunk       |
| Desirable  | Desirable   | Trunk       |
| Trunk      | Auto        | Trunk       |
| Access     | Any         | Access      |

## Implementation

### Basic Configuration

1. **Mode Configuration**
   ```
   ! Configure trunk mode
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode trunk
   
   ! Configure auto mode
   Switch(config-if)# switchport mode dynamic auto
   
   ! Configure desirable mode
   Switch(config-if)# switchport mode dynamic desirable
   ```

2. **Disable DTP**
   ```
   ! Disable DTP negotiation
   Switch(config-if)# switchport nonegotiate
   
   ! Force access mode
   Switch(config-if)# switchport mode access
   ```

### Advanced Configuration

1. **Trunk Encapsulation**
   ```
   ! Configure trunk encapsulation
   Switch(config-if)# switchport trunk encapsulation dot1q
   
   ! Configure allowed VLANs
   Switch(config-if)# switchport trunk allowed vlan 10,20,30
   ```

2. **Native VLAN**
   ```
   ! Configure native VLAN
   Switch(config-if)# switchport trunk native vlan 99
   ```

## Design Considerations

### Network Planning
1. **Port Mode Selection**
   - Access vs Trunk needs
   - Negotiation requirements
   - Security considerations
   - Performance impact

2. **Trunk Design**
   - VLAN requirements
   - Bandwidth needs
   - Native VLAN planning
   - Load distribution

3. **Security Impact**
   - VLAN hopping risks
   - Negotiation exposure
   - Native VLAN security
   - Access control

### Performance Considerations
1. **Negotiation Impact**
   - Protocol overhead
   - Convergence time
   - Link stability
   - CPU utilization

2. **Trunk Efficiency**
   - VLAN pruning
   - Allowed VLANs
   - Load balancing
   - Link utilization

## Troubleshooting

### Common Issues
1. **Negotiation Problems**
   - Mode mismatches
   - Encapsulation mismatches
   - Native VLAN mismatches
   - Timer issues

2. **Trunk Formation**
   - Failed negotiations
   - Incorrect VLANs
   - Protocol errors
   - Hardware limitations

3. **Performance Issues**
   - High CPU usage
   - Link instability
   - Bandwidth problems
   - VLAN issues

### Verification Commands
```
show interfaces trunk
show dtp
show interfaces switchport
show running-config interface
debug sw-vlan dtp
show dtp statistics
```

## Best Practices

### Design Guidelines
1. **Mode Selection**
   ```
   ! Disable DTP on access ports
   Switch(config-if)# switchport mode access
   Switch(config-if)# switchport nonegotiate
   
   ! Configure trunk ports explicitly
   Switch(config-if)# switchport mode trunk
   Switch(config-if)# switchport nonegotiate
   ```

2. **Security Configuration**
   ```
   ! Protect against VLAN hopping
   Switch(config-if)# switchport trunk native vlan 999
   Switch(config-if)# switchport trunk allowed vlan 10-20
   ```

### Operational Procedures
1. **Monitoring**
   - Regular status checks
   - Performance monitoring
   - Error detection
   - Configuration verification

2. **Documentation**
   - Port configurations
   - VLAN assignments
   - Trunk links
   - Mode settings

## Security Considerations

### DTP Security
1. **Disable Negotiation**
   ```
   ! Disable DTP on all ports
   Switch(config-if)# switchport nonegotiate
   ```

2. **Native VLAN Protection**
   ```
   ! Secure native VLAN
   Switch(config)# vlan dot1q tag native
   Switch(config-if)# switchport trunk native vlan 999
   ```

## Interview Tips
- Understand DTP operation
- Know port modes and combinations
- Explain negotiation process
- Understand security implications
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Security features
  - Best practices
- Real-world scenarios:
  - Enterprise networks
  - Data center design
  - Security implementation
  - Trunk configuration
- Best practices:
  - Mode selection
  - Security measures
  - Performance optimization
  - Documentation
- Advanced concepts:
  - VLAN hopping prevention
  - Native VLAN security
  - Trunk optimization
  - Protocol overhead 