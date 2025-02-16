# LACP (Link Aggregation Control Protocol)

## Overview
LACP (IEEE 802.3ad) is a protocol that controls the bundling of multiple physical ports into a single logical channel, providing increased bandwidth and redundancy.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Standard: IEEE 802.3ad
- Port Maximum: 8 active + 8 standby
- LACP PDU: Every 30 seconds (slow) or 1 second (fast)

### LACP Parameters

1. **System Priority**
   - Default: 32768
   - Range: 1-65535
   - Lower value = higher priority

2. **Port Priority**
   - Default: 32768
   - Range: 1-65535
   - Determines active/standby status

3. **LACP Mode**
   - Active: Initiates LACP
   - Passive: Responds to LACP
   - Both ends must be enabled

### Port Channel Characteristics

1. **Load Balancing Methods**
   - Source MAC
   - Destination MAC
   - Source-Destination MAC
   - Source IP
   - Destination IP
   - Source-Destination IP
   - Layer 4 Port
   - Enhanced methods (platform-specific)

2. **Channel Requirements**
   - Consistent speed
   - Matching duplex
   - Same VLAN configuration
   - Compatible port types

## Implementation

### Basic Configuration

1. **Enable LACP**
   ```
   ! Cisco IOS
   Switch(config)# interface range gi1/0/1-4
   Switch(config-if-range)# channel-protocol lacp
   Switch(config-if-range)# channel-group 1 mode active
   
   ! Configure port-channel interface
   Switch(config)# interface port-channel 1
   Switch(config-if)# switchport mode trunk
   ```

2. **Load Balancing Configuration**
   ```
   ! Configure load-balancing method
   Switch(config)# port-channel load-balance src-dst-ip
   
   ! Verify configuration
   Switch# show etherchannel load-balance
   ```

### Advanced Configuration

1. **LACP System Priority**
   ```
   Switch(config)# lacp system-priority 4096
   ```

2. **Port Priority**
   ```
   Switch(config-if)# lacp port-priority 1024
   ```

3. **LACP Timer**
   ```
   Switch(config-if)# lacp rate fast
   ```

## Design Considerations

### Capacity Planning
1. **Bandwidth Requirements**
   - Current utilization
   - Growth projections
   - Traffic patterns

2. **Link Selection**
   - Number of links
   - Speed matching
   - Path diversity

3. **High Availability**
   - Active/Standby links
   - Cross-switch configuration
   - Failure scenarios

### Load Balancing Strategy
1. **Traffic Analysis**
   - Flow patterns
   - Protocol distribution
   - Application requirements

2. **Algorithm Selection**
   - Platform capabilities
   - Traffic type
   - Performance requirements

## Troubleshooting

### Common Issues
1. **Port Channel Formation**
   - Mismatched configurations
   - Speed/duplex mismatch
   - VLAN inconsistencies

2. **Load Balancing**
   - Uneven traffic distribution
   - Suboptimal algorithm
   - Flow polarization

3. **Performance Problems**
   - Link failures
   - Protocol negotiation
   - Hardware limitations

### Verification Commands
```
show lacp neighbor
show lacp internal
show etherchannel summary
show etherchannel load-balance
show lacp counters
debug lacp all
```

## Best Practices

### Design Guidelines
1. **Port Selection**
   - Use same-speed interfaces
   - Consider physical paths
   - Plan for redundancy

2. **Configuration**
   - Consistent settings
   - Document load-balancing
   - Monitor performance

3. **Maintenance**
   - Regular verification
   - Performance monitoring
   - Configuration backup

### High Availability
1. **Multi-chassis Configuration**
   - Cross-stack EtherChannel
   - Virtual switching system
   - Stackwise virtual

2. **Failure Handling**
   - Fast detection
   - Automatic failover
   - Minimal disruption

## Security Considerations

### Port Security
1. **Access Control**
   ```
   Switch(config-if)# switchport port-security
   Switch(config-if)# switchport port-security maximum 1
   ```

2. **Storm Control**
   ```
   Switch(config-if)# storm-control broadcast level 50
   Switch(config-if)# storm-control multicast level 50
   ```

### Monitoring
1. **SNMP Monitoring**
   - Link status
   - Traffic statistics
   - Error counters

2. **Syslog Configuration**
   - Link state changes
   - Protocol events
   - Error conditions

## Interview Tips
- Understand LACP operation principles
- Know configuration requirements
- Explain load-balancing methods
- Understand failure scenarios
- Be familiar with:
  - Configuration commands
  - Troubleshooting steps
  - Design considerations
  - Platform limitations
- Real-world scenarios:
  - Data center deployment
  - Server connectivity
  - Distribution layer design
- Best practices:
  - Link selection
  - Load balancing
  - High availability
- Performance considerations:
  - Traffic distribution
  - Link utilization
  - Failure recovery 