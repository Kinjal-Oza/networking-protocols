# STP (Spanning Tree Protocol)

## Overview
Spanning Tree Protocol (IEEE 802.1D) is a Layer 2 protocol that prevents loops in networks with redundant paths by creating a loop-free logical topology while maintaining backup paths for redundancy.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- Standard: IEEE 802.1D
- Convergence Time: 30-50 seconds
- BPDU Type: Configuration and TCN

### Bridge Protocol Data Unit (BPDU)
1. **Configuration BPDU Fields**
   - Protocol ID (2 bytes)
   - Version (1 byte)
   - Message Type (1 byte)
   - Flags (1 byte)
   - Root Bridge ID (8 bytes)
   - Root Path Cost (4 bytes)
   - Bridge ID (8 bytes)
   - Port ID (2 bytes)
   - Message Age (2 bytes)
   - Max Age (2 bytes)
   - Hello Time (2 bytes)
   - Forward Delay (2 bytes)

2. **Bridge ID Components**
   - Priority (2 bytes): 0-65535
   - System ID Extension (12 bits)
   - MAC Address (6 bytes)

### Port States
1. **Disabled**
   - Port administratively down
   - No BPDUs processed
   - No data forwarding

2. **Blocking**
   - Initial state
   - Receives BPDUs
   - No data forwarding
   - No MAC learning

3. **Listening**
   - Processes BPDUs
   - No data forwarding
   - No MAC learning
   - Duration: Forward Delay (15s)

4. **Learning**
   - Processes BPDUs
   - No data forwarding
   - MAC learning active
   - Duration: Forward Delay (15s)

5. **Forwarding**
   - Processes BPDUs
   - Data forwarding active
   - MAC learning active
   - Normal operation

### Port Roles
1. **Root Port**
   - Best path to root bridge
   - One per non-root bridge
   - Lowest root path cost

2. **Designated Port**
   - Best path from segment to root
   - One per segment
   - Forwards BPDUs

3. **Non-Designated Port**
   - Alternate path to root
   - Blocking state
   - Backup path

4. **Disabled Port**
   - Administratively down
   - No participation in STP

### Root Bridge Election
1. **Selection Criteria**
   - Lowest Bridge ID wins
   - Priority (0-65535)
   - MAC Address tiebreaker

2. **Default Values**
   - Priority: 32768
   - Hello Time: 2 seconds
   - Forward Delay: 15 seconds
   - Max Age: 20 seconds

### Path Cost Calculation
1. **Default Costs**
   - 10 Mbps: 100
   - 100 Mbps: 19
   - 1 Gbps: 4
   - 10 Gbps: 2

2. **Path Selection**
   - Lowest root path cost
   - Lowest sender bridge ID
   - Lowest port ID

## Implementation

### Basic Configuration
1. **Cisco IOS**
   ```
   ! Set bridge priority
   Switch(config)# spanning-tree vlan 1 priority 4096
   
   ! Enable BPDU guard
   Switch(config-if)# spanning-tree bpduguard enable
   
   ! Enable portfast
   Switch(config-if)# spanning-tree portfast
   ```

2. **Port Cost Configuration**
   ```
   Switch(config-if)# spanning-tree cost 100
   Switch(config-if)# spanning-tree port-priority 64
   ```

### Root Bridge Configuration
```
! Primary root
Switch(config)# spanning-tree vlan 1 root primary

! Secondary root
Switch(config)# spanning-tree vlan 1 root secondary
```

## Security Considerations

### BPDU Guard
```
! Global configuration
Switch(config)# spanning-tree portfast bpduguard default

! Interface configuration
Switch(config-if)# spanning-tree bpduguard enable
```

### Root Guard
```
Switch(config-if)# spanning-tree guard root
```

### Loop Guard
```
! Global configuration
Switch(config)# spanning-tree loopguard default

! Interface configuration
Switch(config-if)# spanning-tree guard loop
```

## Troubleshooting

### Common Issues
1. **Topology Changes**
   - Link flapping
   - New device addition
   - Port state changes

2. **Root Bridge Problems**
   - Suboptimal root location
   - Rogue root bridge
   - Priority conflicts

3. **Convergence Issues**
   - Slow convergence
   - Port stuck in state
   - BPDU problems

### Verification Commands
```
show spanning-tree
show spanning-tree root
show spanning-tree blockedports
show spanning-tree inconsistentports
show spanning-tree interface
```

## Best Practices

### Design Considerations
1. **Root Bridge Placement**
   - Central location
   - High bandwidth links
   - Redundant power

2. **Diameter Planning**
   - Maximum 7 switches
   - Consider convergence time
   - Backup paths

3. **Security Features**
   - BPDU Guard
   - Root Guard
   - Loop Guard

### Performance Optimization
1. **Portfast**
   - Access ports only
   - End-device connections
   - Quick convergence

2. **Uplink Fast**
   - Immediate failover
   - Backup path utilization
   - Layer 2 only

3. **Backbone Fast**
   - Indirect link failure
   - Reduces convergence time
   - Max Age timer bypass

## Interview Tips
- Understand STP operation
- Know port states and roles
- Explain root bridge election
- Understand convergence process
- Be familiar with:
  - BPDU format
  - Path cost calculation
  - Security features
  - Optimization options
- Real-world scenarios:
  - Loop prevention
  - Redundancy design
  - Troubleshooting
- Best practices:
  - Root bridge placement
  - Security implementation
  - Performance tuning 