# CDP (Cisco Discovery Protocol)

## Overview
CDP is a Cisco proprietary Layer 2 protocol that discovers directly connected Cisco equipment and gathers information about neighboring devices. It enables network administrators to view and troubleshoot network topology and device configurations.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Multicast Address: 01:00:0C:CC:CC:CC
- Protocol Type: 0x2000
- Default Timer: 60 seconds
- Hold Time: 180 seconds
- Cisco proprietary

### CDP Information

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
   - Interface IP
   - Platform
   - VTP domain

3. **Network Information**
   - Management addresses
   - VLAN information
   - MTU
   - Trust settings
   - Power requirements

### CDP Packet Format

1. **Header**
   - Version
   - Time to live
   - Checksum
   - Type/Length/Value (TLV)

2. **TLV Fields**
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

1. **Enable/Disable CDP**
   ```
   ! Enable CDP globally
   Router(config)# cdp run
   
   ! Disable CDP globally
   Router(config)# no cdp run
   
   ! Enable/Disable CDP on interface
   Router(config-if)# cdp enable
   Router(config-if)# no cdp enable
   ```

2. **Timer Configuration**
   ```
   ! Configure CDP timers
   Router(config)# cdp timer 30
   Router(config)# cdp holdtime 120
   ```

### Advanced Configuration

1. **CDP Filtering**
   ```
   ! Configure CDP filtering
   Router(config)# cdp filter-tlv port-id
   Router(config)# cdp filter-tlv version
   
   ! Configure interface filtering
   Router(config-if)# no cdp enable
   ```

2. **CDP Version**
   ```
   ! Configure CDP version
   Router(config)# cdp advertise-v2
   
   ! Configure interface version
   Router(config-if)# cdp version 2
   ```

## Design Considerations

### Network Planning
1. **Security Impact**
   - Information disclosure
   - Attack surface
   - Network visibility
   - Management access

2. **Performance Impact**
   - CPU utilization
   - Bandwidth usage
   - Timer settings
   - Network size

3. **Management Requirements**
   - Topology discovery
   - Device inventory
   - Troubleshooting needs
   - Documentation

### Implementation Strategy
1. **Deployment Scope**
   - Global vs interface
   - Timer optimization
   - Version selection
   - Filtering requirements

2. **Security Strategy**
   - Access control
   - Information filtering
   - Interface selection
   - Monitoring setup

## Troubleshooting

### Common Issues
1. **Discovery Problems**
   - Missing neighbors
   - Incomplete information
   - Version mismatch
   - Timer issues

2. **Security Concerns**
   - Information leakage
   - Unauthorized access
   - Attack vectors
   - Policy violations

3. **Performance Issues**
   - High CPU usage
   - Network congestion
   - Memory utilization
   - Timer conflicts

### Verification Commands
```bash
# Show CDP information
show cdp
show cdp neighbors
show cdp neighbors detail
show cdp entry *

# Debug CDP operations
debug cdp packets
debug cdp events
debug cdp adjacency

# Show interface CDP
show cdp interface
show cdp traffic
```

## Best Practices

### Design Guidelines
1. **Timer Configuration**
   ```
   ! Configure optimal timers
   Router(config)# cdp timer 60
   Router(config)# cdp holdtime 180
   ```

2. **Security Settings**
   ```
   ! Configure secure settings
   Router(config)# no cdp run
   Router(config)# interface range gi0/1 - 24
   Router(config-if-range)# no cdp enable
   ```

### Operational Procedures
1. **Monitoring Setup**
   - Regular verification
   - Neighbor tracking
   - Event logging
   - Performance monitoring

2. **Documentation**
   - Network topology
   - Device inventory
   - Configuration details
   - Security policies

## Security Considerations

### CDP Security
1. **Access Control**
   ```
   ! Disable CDP on external interfaces
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# no cdp enable
   
   ! Filter sensitive information
   Router(config)# cdp filter-tlv version
   Router(config)# cdp filter-tlv platform
   ```

2. **Monitoring**
   ```
   ! Configure logging
   Router(config)# logging trap notifications
   Router(config)# logging host 192.168.1.100
   ```

## Interview Tips
- Understand CDP operation
- Know information gathered
- Explain security implications
- Understand configuration options
- Be familiar with:
  - Protocol details
  - Troubleshooting methods
  - Security features
  - Best practices
- Real-world scenarios:
  - Network discovery
  - Topology mapping
  - Troubleshooting
  - Security assessment
- Best practices:
  - Selective deployment
  - Security measures
  - Timer optimization
  - Documentation
- Advanced concepts:
  - Version differences
  - TLV fields
  - Filtering options
  - Integration capabilities
- Comparison with LLDP:
  - Standards-based alternative
  - Feature differences
  - Interoperability
  - Configuration syntax 