# UDLD (UniDirectional Link Detection)

## Overview
UDLD is a Cisco proprietary Layer 2 protocol that monitors the physical configuration of fiber optic and copper Ethernet cables and detects when a unidirectional link exists. When a unidirectional link is detected, UDLD shuts down the affected interface to prevent network issues like spanning-tree loops.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Cisco Proprietary
- Message Interval: 15 seconds (default)
- Message Timeout: 45 seconds (default)
- Multicast MAC: 01:00:0C:CC:CC:CC
- Protocol Type: 0x0111

### UDLD Modes

1. **Normal Mode**
   - Detects unidirectional links
   - Logs error messages
   - Does not disable ports
   - Requires manual intervention

2. **Aggressive Mode**
   - Detects unidirectional links
   - Sends additional probes
   - Automatically disables ports
   - Eight failed retries required

### UDLD States

1. **Port States**
   - Undetermined
   - Bidirectional
   - Shutdown
   - Not applicable

2. **Message Types**
   - Probe
   - Echo
   - Flush
   - Detection state

## Implementation

### Basic Configuration

1. **Enable UDLD**
   ```
   ! Enable UDLD globally
   Switch(config)# udld enable
   
   ! Enable aggressive mode globally
   Switch(config)# udld aggressive
   
   ! Enable on specific interface
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# udld port
   Switch(config-if)# udld port aggressive
   ```

2. **Timer Configuration**
   ```
   ! Configure message interval
   Switch(config)# udld message time 15
   
   ! Configure recovery interval
   Switch(config)# udld recovery interval 60
   ```

### Advanced Configuration

1. **Recovery Configuration**
   ```
   ! Enable auto-recovery
   Switch(config)# udld recovery
   
   ! Configure recovery timer
   Switch(config)# udld recovery interval 60
   ```

2. **Reset Configuration**
   ```
   ! Reset all UDLD ports
   Switch# udld reset
   
   ! Reset specific interface
   Switch# udld reset interface GigabitEthernet0/1
   ```

## Design Considerations

### Network Planning
1. **Link Types**
   - Fiber optic links
   - Copper links
   - Point-to-point
   - Shared media

2. **Mode Selection**
   - Normal vs Aggressive
   - Recovery options
   - Timer settings
   - Interface types

3. **Performance Impact**
   - Message overhead
   - Processing requirements
   - Recovery time
   - Link stability

### High Availability
1. **Recovery Planning**
   - Auto-recovery
   - Manual recovery
   - Notification system
   - Backup paths

2. **Monitoring Strategy**
   - Link status
   - Error detection
   - Performance metrics
   - State changes

## Troubleshooting

### Common Issues
1. **Detection Problems**
   - False positives
   - Missed detections
   - Timer issues
   - Echo failures

2. **Recovery Issues**
   - Port stuck shutdown
   - Recovery failures
   - Reset problems
   - Timer mismatches

3. **Configuration Problems**
   - Mode mismatch
   - Timer mismatch
   - Recovery settings
   - Interface compatibility

### Verification Commands
```
show udld
show udld interface
show udld neighbors
show udld fast-hello statistics
debug udld events
debug udld packets
debug udld registries
```

## Best Practices

### Design Guidelines
1. **Mode Selection**
   ```
   ! Configure aggressive mode on critical links
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# udld port aggressive
   
   ! Configure normal mode on standard links
   Switch(config)# interface range GigabitEthernet0/2-24
   Switch(config-if-range)# udld port
   ```

2. **Timer Configuration**
   ```
   ! Configure optimal timers
   Switch(config)# udld message time 15
   Switch(config)# udld recovery interval 60
   ```

### Operational Procedures
1. **Monitoring Setup**
   - Regular verification
   - State monitoring
   - Error tracking
   - Performance analysis

2. **Documentation**
   - Port configuration
   - Mode selection
   - Recovery procedures
   - Troubleshooting guide

## Security Considerations

### UDLD Security
1. **Port Protection**
   ```
   ! Configure port security with UDLD
   Switch(config-if)# switchport port-security
   Switch(config-if)# udld port aggressive
   ```

2. **Monitoring**
   ```
   ! Configure logging
   Switch(config)# logging trap notifications
   Switch(config)# logging host 192.168.1.100
   ```

## Interview Tips
- Understand UDLD operation
- Know operation modes
- Explain detection process
- Understand recovery options
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Recovery procedures
  - Best practices
- Real-world scenarios:
  - Fiber optic links
  - Data center connections
  - Critical infrastructure
  - Loop prevention
- Best practices:
  - Mode selection
  - Timer configuration
  - Recovery setup
  - Documentation
- Advanced concepts:
  - Fiber vs copper
  - Integration with STP
  - Recovery mechanisms
  - Performance impact 