# LLDP (Link Layer Discovery Protocol)

## Overview
LLDP is an IEEE 802.1AB vendor-neutral link layer protocol that allows network devices to advertise their identity, capabilities, and neighbors on a local area network. Unlike CDP, LLDP is an open standard and works across multi-vendor networks.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- IEEE Standard: 802.1AB
- Multicast MAC: 01:80:C2:00:00:0E
- EtherType: 0x88CC
- Default Timer: 30 seconds
- Hold Time: 120 seconds

### LLDP Information

1. **Mandatory TLVs**
   - Chassis ID
   - Port ID
   - Time to Live
   - End of LLDPDU

2. **Optional TLVs**
   - Port Description
   - System Name
   - System Description
   - System Capabilities
   - Management Address

3. **Organization Specific TLVs**
   - IEEE 802.1 (VLANs, protocols)
   - IEEE 802.3 (Power, MTU, speed)
   - IETF (Management addresses)
   - TIA (LLDP-MED capabilities)

### LLDP-MED Extensions

1. **Capabilities**
   - Network Policy
   - Location Identification
   - Extended Power via MDI
   - Inventory Management

2. **Device Types**
   - Class I: Generic Endpoint
   - Class II: Media Endpoint
   - Class III: Communication Device
   - Network Connectivity Device

## Implementation

### Basic Configuration

1. **Enable/Disable LLDP**
   ```
   ! Enable LLDP globally
   Switch(config)# lldp run
   
   ! Disable LLDP globally
   Switch(config)# no lldp run
   
   ! Enable/Disable LLDP on interface
   Switch(config-if)# lldp transmit
   Switch(config-if)# lldp receive
   ```

2. **Timer Configuration**
   ```
   ! Configure LLDP timers
   Switch(config)# lldp timer 30
   Switch(config)# lldp holdtime 120
   Switch(config)# lldp reinit 2
   ```

### Advanced Configuration

1. **TLV Selection**
   ```
   ! Configure TLV options
   Switch(config)# lldp tlv-select system-name
   Switch(config)# lldp tlv-select system-description
   Switch(config)# lldp tlv-select system-capabilities
   Switch(config)# lldp tlv-select management-address
   ```

2. **LLDP-MED Configuration**
   ```
   ! Configure LLDP-MED
   Switch(config)# lldp run
   Switch(config-if)# lldp med-tlv-select capabilities
   Switch(config-if)# lldp med-tlv-select network-policy
   Switch(config-if)# lldp med-tlv-select power-management
   ```

## Design Considerations

### Network Planning
1. **Deployment Scope**
   - Global vs interface
   - TLV selection
   - Timer settings
   - Network size

2. **Performance Impact**
   - CPU utilization
   - Bandwidth usage
   - Memory requirements
   - Update frequency

3. **Integration Requirements**
   - Multi-vendor support
   - Management systems
   - Monitoring tools
   - Documentation needs

### Implementation Strategy
1. **Rollout Planning**
   - Phased deployment
   - Testing procedures
   - Verification steps
   - Fallback options

2. **Management Strategy**
   - Monitoring setup
   - Data collection
   - Analysis tools
   - Reporting requirements

## Troubleshooting

### Common Issues
1. **Discovery Problems**
   - Missing neighbors
   - Incomplete information
   - Timer issues
   - TLV problems

2. **Integration Issues**
   - Vendor compatibility
   - TLV support
   - MED capabilities
   - Management systems

3. **Performance Problems**
   - High CPU usage
   - Network congestion
   - Memory utilization
   - Timer conflicts

### Verification Commands
```bash
# Show LLDP information
show lldp
show lldp neighbors
show lldp neighbors detail
show lldp interface

# Show LLDP traffic
show lldp traffic
show lldp errors

# Show LLDP-MED
show lldp med
show lldp med neighbors
```

## Best Practices

### Design Guidelines
1. **Timer Configuration**
   ```
   ! Configure optimal timers
   Switch(config)# lldp timer 30
   Switch(config)# lldp holdtime 120
   Switch(config)# lldp reinit 2
   ```

2. **TLV Selection**
   ```
   ! Configure essential TLVs
   Switch(config)# lldp tlv-select system-name
   Switch(config)# lldp tlv-select system-capabilities
   Switch(config)# lldp tlv-select port-description
   ```

### Operational Procedures
1. **Monitoring Setup**
   - Regular verification
   - Neighbor tracking
   - Performance monitoring
   - Error logging

2. **Documentation**
   - Network topology
   - Device inventory
   - Configuration details
   - TLV requirements

## Security Considerations

### LLDP Security
1. **Access Control**
   ```
   ! Disable LLDP on external interfaces
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# no lldp transmit
   Switch(config-if)# no lldp receive
   ```

2. **Information Control**
   ```
   ! Limit TLV advertisement
   Switch(config)# no lldp tlv-select management-address
   Switch(config)# no lldp tlv-select system-description
   ```

## Interview Tips
- Understand LLDP operation
- Know TLV types and usage
- Explain LLDP-MED features
- Understand configuration options
- Be familiar with:
  - Protocol details
  - Troubleshooting methods
  - Security features
  - Best practices
- Real-world scenarios:
  - Network discovery
  - VoIP deployment
  - Multi-vendor networks
  - Asset management
- Best practices:
  - TLV selection
  - Timer configuration
  - Security measures
  - Documentation
- Advanced concepts:
  - LLDP-MED
  - Organization-specific TLVs
  - Power management
  - Location services
- Comparison with CDP:
  - Vendor neutrality
  - Feature differences
  - Interoperability
  - Configuration options 