# RSTP (Rapid Spanning Tree Protocol)

## Overview
RSTP (IEEE 802.1w) is an evolution of STP that provides significantly faster convergence while maintaining loop prevention. It reduces network recovery time from 30-50 seconds to less than 6 seconds.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Standard: IEEE 802.1w
- Convergence Time: 1-6 seconds
- Backward compatible with STP

### Key Improvements over STP
1. **Faster Convergence**
   - Direct handshake mechanism
   - Rapid role transitions
   - Backup path utilization

2. **Port States**
   - Reduced from 5 to 3 states
   - Discarding (combines blocking/listening)
   - Learning
   - Forwarding

3. **Port Roles**
   - Root Port
   - Designated Port
   - Alternate Port
   - Backup Port

### RSTP Port Types
1. **Edge Ports**
   - Connects to end devices
   - Immediate transition to forwarding
   - Replaces STP PortFast
   - BPDU Guard recommended

2. **Point-to-Point Ports**
   - Switch-to-switch connections
   - Full-duplex links
   - Rapid transition capable

3. **Shared Ports**
   - Half-duplex connections
   - Legacy hub connections
   - Slower convergence

## Implementation

### Basic Configuration
1. **Enable RSTP**
   ```
   ! Cisco IOS
   Switch(config)# spanning-tree mode rapid-pvst
   
   ! Enable edge port
   Switch(config-if)# spanning-tree portfast edge
   
   ! Configure point-to-point link
   Switch(config-if)# spanning-tree link-type point-to-point
   ```

2. **Port Priority and Cost**
   ```
   ! Set port priority
   Switch(config-if)# spanning-tree port-priority 16
   
   ! Set path cost
   Switch(config-if)# spanning-tree cost 100
   ```

### RSTP Timers
1. **Hello Time**
   ```
   Switch(config)# spanning-tree vlan 1 hello-time 2
   ```

2. **Forward Delay**
   ```
   Switch(config)# spanning-tree vlan 1 forward-time 15
   ```

3. **Max Age**
   ```
   Switch(config)# spanning-tree vlan 1 max-age 20
   ```

## Synchronization Process

### Proposal-Agreement Mechanism
1. **Proposal Phase**
   - Superior BPDU received
   - Port becomes root port
   - Sends proposal to downstream switch

2. **Agreement Phase**
   - Downstream switch blocks non-edge ports
   - Sends agreement to upstream switch
   - Rapid transition to forwarding

### Link Failure Recovery
1. **Direct Link Failure**
   - Immediate detection
   - Alternate port activation
   - No waiting for timer expiry

2. **Indirect Link Failure**
   - Inferior BPDU detection
   - Rapid role calculation
   - Fast convergence

## Security Considerations

### BPDU Protection
```
! Enable BPDU Guard
Switch(config-if)# spanning-tree bpduguard enable

! Enable BPDU Filter
Switch(config-if)# spanning-tree bpdufilter enable
```

### Root Protection
```
! Enable Root Guard
Switch(config-if)# spanning-tree guard root

! Enable Loop Guard
Switch(config-if)# spanning-tree guard loop
```

## Troubleshooting

### Common Issues
1. **Compatibility Problems**
   - Mixed STP/RSTP environments
   - Port type misconfigurations
   - Link type mismatches

2. **Convergence Issues**
   - Incorrect port roles
   - BPDU handling problems
   - Timer misconfigurations

3. **Port State Issues**
   - Stuck in discarding
   - Unexpected transitions
   - Edge port problems

### Verification Commands
```
show spanning-tree
show spanning-tree interface
show spanning-tree summary
show spanning-tree detail
debug spanning-tree rstp
```

## Best Practices

### Design Guidelines
1. **Topology Planning**
   - Identify edge ports
   - Configure point-to-point links
   - Plan redundant paths

2. **Performance Optimization**
   - Use edge ports where appropriate
   - Enable BPDU protection
   - Configure correct link types

3. **Migration Strategy**
   - Phased RSTP deployment
   - Version compatibility testing
   - Fallback planning

### Common Deployments
1. **Enterprise Campus**
   - Edge ports for access layer
   - Point-to-point for distribution
   - Root bridge placement

2. **Data Center**
   - Server connections as edge ports
   - Redundant paths
   - Fast convergence requirements

## Interview Tips
- Understand RSTP improvements over STP
- Know port states and roles
- Explain proposal-agreement mechanism
- Understand convergence process
- Be familiar with:
  - Configuration commands
  - Security features
  - Troubleshooting methods
  - Migration strategies
- Real-world scenarios:
  - Network upgrades
  - Convergence requirements
  - Security implementations
- Best practices:
  - Port type selection
  - Protection mechanisms
  - Performance optimization 