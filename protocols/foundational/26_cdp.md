# CDP (Cisco Discovery Protocol)

## Overview
CDP is a Cisco proprietary Layer 2 protocol that discovers directly connected Cisco equipment and gathers information about neighboring devices. It enables network administrators to view and troubleshoot network topology and device relationships without requiring Layer 3 connectivity.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Cisco Proprietary
- Multicast Address: 01:00:0C:CC:CC:CC
- Protocol Type: 0x2000
- Default Timer: 60 seconds
- Default Holdtime: 180 seconds

### CDP Information Categories

1. **Device Information**
   - Device ID
   - Software version
   - Platform
   - Capabilities
   - Native VLAN
   - Duplex setting

2. **Interface Information**
   - Local interface
   - Port ID
   - Interface speed
   - MTU
   - Encapsulation type
   - Port VLAN

3. **Network Information**
   - Management addresses
   - VTP domain
   - Power requirements
   - Voice VLAN

### CDP Packet Format
1. **Header Fields**
   - Version
   - Time-to-live
   - Checksum
   - Type/Length/Value (TLV)

2. **TLV Types**
   - Device ID
   - Addresses
   - Port ID
   - Capabilities
   - Version
   - Platform
   - Protocol
   - Power

## Implementation

### Basic Configuration

1. **Global CDP Configuration**
   ```
   ! Enable/Disable CDP globally
   Router(config)# cdp run
   Router(config)# no cdp run
   
   ! Adjust timers
   Router(config)# cdp timer 30
   Router(config)# cdp holdtime 120
   ```

2. **Interface Configuration**
   ```
   ! Enable/Disable CDP on interface
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# cdp enable
   Router(config-if)# no cdp enable
   ```

### Advanced Configuration

1. **CDP Filtering**
   ```
   ! Configure CDP filter
   Router(config)# cdp filter-list 1 deny
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# cdp filter-list 1
   ```

2. **Version Control**
   ```
   ! Configure CDP version
   Router(config)# cdp advertise-v2
   Router(config)# no cdp advertise-v2
   ```

## Design Considerations

### Network Planning
1. **Discovery Strategy**
   - Interface selection
   - Timer optimization
   - Information filtering
   - Resource usage

2. **Scalability**
   - Network size
   - Update frequency
   - Cache size
   - Processing overhead

3. **Security Impact**
   - Information disclosure
   - Attack surface
   - Access control
   - Monitoring requirements

### Management Integration
1. **NMS Integration**
   - Topology mapping
   - Asset management
   - Change detection
   - Documentation

2. **Monitoring Strategy**
   - Performance impact
   - Resource utilization
   - Error detection
   - Change tracking

## Troubleshooting

### Common Issues
1. **Discovery Problems**
   - Missing neighbors
   - Incomplete information
   - Version mismatch
   - Timer issues

2. **Performance Issues**
   - High CPU usage
   - Memory consumption
   - Network overhead
   - Update delays

3. **Security Concerns**
   - Information leakage
   - Unauthorized access
   - Protocol abuse
   - Attack vectors

### Verification Commands
```
show cdp
show cdp neighbors
show cdp neighbors detail
show cdp interface
show cdp traffic
show cdp entry *
debug cdp packets
debug cdp events
```

## Best Practices

### Design Guidelines
1. **Implementation Strategy**
   - Selective enablement
   - Timer tuning
   - Version control
   - Filter implementation

2. **Security Measures**
   - Interface restrictions
   - Information filtering
   - Access control
   - Monitoring setup

3. **Performance Optimization**
   - Resource management
   - Update frequency
   - Cache maintenance
   - Process priority

### Operational Procedures
1. **Maintenance**
   - Regular verification
   - Cache cleanup
   - Version updates
   - Documentation

2. **Monitoring**
   - Neighbor changes
   - Resource usage
   - Error detection
   - Security events

## Security Considerations

### Protocol Security
1. **Access Control**
   ```
   ! Configure CDP ACL
   Router(config)# access-list 100 deny 0x2000 any any
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# cdp filter-list 100
   ```

2. **Interface Restriction**
   ```
   ! Disable CDP on external interfaces
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# no cdp enable
   ```

## Interview Tips
- Understand CDP operation
- Know information types
- Explain discovery process
- Understand security implications
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Network discovery
  - Topology mapping
  - Asset management
  - Troubleshooting
- Best practices:
  - Selective enablement
  - Security implementation
  - Performance tuning
  - Documentation
- Advanced concepts:
  - TLV structure
  - Version differences
  - Integration options
  - Security measures 