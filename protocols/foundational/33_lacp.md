# LACP (Link Aggregation Control Protocol)

## Overview
LACP is an IEEE 802.3ad standard protocol that allows the bundling of several physical ports to form a single logical channel. It provides a method to control the bundling of several physical ports together to form a single logical channel, enabling increased bandwidth and redundancy.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- IEEE Standard: 802.3ad
- Multicast MAC: 01:80:C2:00:00:02
- Protocol Type: 0x8809
- LACP PDU Rate: Fast (1s) or Slow (30s)
- Maximum Links: 8 active, 8 standby

### LACP Modes

1. **Active Mode**
   - Initiates LACP negotiation
   - Sends LACP packets
   - Forms bundle if possible
   - Recommended for important links

2. **Passive Mode**
   - Responds to LACP packets
   - Does not initiate
   - Forms bundle if requested
   - Default on some platforms

3. **On Mode**
   - Static configuration
   - No LACP packets
   - Manual bundling
   - No negotiation

### Mode Combinations
| Local Port | Remote Port | Result      |
|------------|-------------|-------------|
| Active     | Active      | Bundle      |
| Active     | Passive     | Bundle      |
| Passive    | Passive     | No Bundle   |
| On         | On          | Bundle      |
| Any        | Off/On      | No Bundle   |

### Channel Types

1. **Layer 2 Link Aggregation**
   - Switching ports
   - VLAN support
   - Load balancing
   - Link redundancy

2. **Layer 3 Link Aggregation**
   - Routing ports
   - IP addressing
   - Protocol routing
   - Load distribution

## Implementation

### Basic Configuration

1. **Interface Configuration**
   ```
   ! Configure Layer 2 LACP
   Switch(config)# interface range GigabitEthernet0/1-2
   Switch(config-if-range)# channel-group 1 mode active
   Switch(config-if-range)# channel-protocol lacp
   
   ! Configure port-channel interface
   Switch(config)# interface port-channel 1
   Switch(config-if)# switchport mode trunk
   ```

2. **Load Balancing Configuration**
   ```
   ! Configure load-balancing method
   Switch(config)# port-channel load-balance src-dst-mac
   
   ! Verify load-balancing
   Switch# show etherchannel load-balance
   ```

### Advanced Configuration

1. **Layer 3 LACP**
   ```
   ! Configure Layer 3 LACP
   Switch(config)# interface range GigabitEthernet0/1-2
   Switch(config-if-range)# no switchport
   Switch(config-if-range)# channel-group 1 mode active
   
   ! Configure port-channel interface
   Switch(config)# interface port-channel 1
   Switch(config-if)# ip address 192.168.1.1 255.255.255.0
   ```

2. **LACP System Priority**
   ```
   ! Configure LACP system priority
   Switch(config)# lacp system-priority 32768
   
   ! Configure port priority
   Switch(config-if)# lacp port-priority 32768
   ```

## Design Considerations

### Network Planning
1. **Link Selection**
   - Speed matching
   - Duplex matching
   - Interface types
   - Load distribution

2. **Bandwidth Planning**
   - Aggregate bandwidth
   - Traffic patterns
   - Growth requirements
   - Redundancy needs

3. **Mode Selection**
   - Active vs Passive
   - Static configuration
   - Protocol requirements
   - Network stability

### High Availability
1. **Redundancy Planning**
   - Link redundancy
   - Device redundancy
   - Failure recovery
   - Load sharing

2. **Performance Optimization**
   - Load balancing method
   - Traffic distribution
   - Link utilization
   - Monitoring strategy

## Troubleshooting

### Common Issues
1. **Bundle Formation**
   - Mode mismatches
   - Speed mismatches
   - Duplex mismatches
   - VLAN mismatches

2. **Performance Issues**
   - Uneven load distribution
   - Link failures
   - Protocol errors
   - Configuration mistakes

3. **Compatibility Issues**
   - Hardware limitations
   - Software versions
   - Feature support
   - Protocol conflicts

### Verification Commands
```
show lacp neighbor
show lacp counters
show lacp sys-id
show etherchannel summary
show interfaces port-channel
debug lacp events
debug lacp packets
```

## Best Practices

### Design Guidelines
1. **Mode Selection**
   ```
   ! Configure active mode for critical links
   Switch(config-if-range)# channel-group 1 mode active
   
   ! Configure passive mode for less critical links
   Switch(config-if-range)# channel-group 2 mode passive
   ```

2. **Load Balancing**
   ```
   ! Configure optimal load balancing
   Switch(config)# port-channel load-balance src-dst-ip
   ```

### Operational Procedures
1. **Monitoring**
   - Regular status checks
   - Performance monitoring
   - Error detection
   - Configuration verification

2. **Documentation**
   - Channel groups
   - Port assignments
   - Load balancing
   - Failure procedures

## Security Considerations

### LACP Security
1. **Access Control**
   ```
   ! Configure port security
   Switch(config-if)# switchport port-security
   Switch(config-if)# switchport port-security maximum 1
   ```

2. **VLAN Security**
   ```
   ! Configure allowed VLANs
   Switch(config-if)# switchport trunk allowed vlan 10,20,30
   ```

## Interview Tips
- Understand LACP operation
- Know mode combinations
- Explain bundle formation
- Understand load balancing
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Data center links
  - Distribution layer
  - Core connectivity
  - Server connections
- Best practices:
  - Mode selection
  - Load balancing
  - Monitoring setup
  - Documentation
- Advanced concepts:
  - Layer 3 link aggregation
  - Load distribution
  - High availability
  - Performance optimization
- Comparison with PAgP:
  - Standard vs proprietary
  - Mode differences
  - Feature comparison
  - Use case selection 