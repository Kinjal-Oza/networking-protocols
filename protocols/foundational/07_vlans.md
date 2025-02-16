# VLANs (Virtual Local Area Networks)

## Overview
VLANs are logical networks that allow network segmentation at Layer 2, enabling the creation of multiple broadcast domains within a single physical network infrastructure.

## Technical Details

### VLAN Characteristics
- Layer 2 network segmentation
- IEEE 802.1Q standard
- 12-bit VLAN ID (1-4094)
- Independent broadcast domains
- Logical network isolation

### VLAN Types

1. **Data VLAN**
   - Regular user traffic
   - Default VLAN (VLAN 1)
   - Multiple data VLANs common

2. **Voice VLAN**
   - Voice traffic prioritization
   - QoS marking
   - Separate from data VLAN

3. **Management VLAN**
   - Network device management
   - Secure administrative access
   - Typically isolated

4. **Native VLAN**
   - Untagged traffic handling
   - Default: VLAN 1
   - Security considerations

### VLAN Configuration

1. **Port Modes**
   - **Access Ports**
     - Single VLAN
     - Untagged traffic
     - End-device connection
   
   - **Trunk Ports**
     - Multiple VLANs
     - Tagged traffic
     - Switch-to-switch links

2. **VLAN Assignment**
   - Static configuration
   - Dynamic (VMPS/RADIUS)
   - Protocol-based
   - MAC-based

3. **VLAN Ranges**
   - Normal: 1-1005
   - Extended: 1006-4094
   - Reserved: 1002-1005

## Implementation

### Switch Configuration
1. **VLAN Creation**
   ```
   Switch(config)# vlan 10
   Switch(config-vlan)# name Engineering
   ```

2. **Access Port**
   ```
   Switch(config)# interface fa0/1
   Switch(config-if)# switchport mode access
   Switch(config-if)# switchport access vlan 10
   ```

3. **Trunk Port**
   ```
   Switch(config)# interface gi0/1
   Switch(config-if)# switchport mode trunk
   Switch(config-if)# switchport trunk allowed vlan 10,20,30
   ```

### Inter-VLAN Routing

1. **Router on a Stick**
   - Single physical interface
   - Subinterfaces for each VLAN
   - 802.1Q encapsulation

2. **Layer 3 Switch**
   - SVI (Switch Virtual Interface)
   - Routing between VLANs
   - Higher performance

3. **Multilayer Switch**
   - Hardware-based routing
   - Low latency
   - High throughput

## Security Considerations

### VLAN Hopping Prevention
1. **DTP Disable**
   ```
   Switch(config-if)# switchport nonegotiate
   ```

2. **Native VLAN**
   - Change from default
   - Match on trunk ports
   - Tag native VLAN

3. **Unused Ports**
   - Shutdown
   - Place in unused VLAN
   - Disable DTP

### Private VLANs
1. **Primary VLAN**
   - Upstream connectivity
   - Contains secondary VLANs

2. **Isolated VLAN**
   - No peer communication
   - Upstream only

3. **Community VLAN**
   - Peer communication within VLAN
   - Upstream communication

## Troubleshooting

### Common Issues
1. **Trunk Issues**
   - Mismatched allowed VLANs
   - Native VLAN mismatch
   - Encapsulation mismatch

2. **Access Port Issues**
   - Wrong VLAN assignment
   - Port mode mismatch
   - VLAN not created

3. **Routing Issues**
   - Missing VLAN interfaces
   - Incorrect IP addressing
   - Route configuration

### Verification Commands
1. **Cisco IOS**
   ```
   show vlan brief
   show interfaces trunk
   show running-config interface
   show spanning-tree vlan
   ```

2. **Troubleshooting**
   ```
   show mac address-table
   show vlan
   show interfaces status
   debug spanning-tree events
   ```

## Best Practices

### Design
1. **VLAN Numbering**
   - Consistent scheme
   - Documentation
   - Room for growth

2. **Network Segmentation**
   - Functional groups
   - Security requirements
   - Traffic patterns

3. **Performance**
   - Broadcast domain size
   - Traffic distribution
   - Trunk capacity

### Management
1. **Documentation**
   - VLAN assignments
   - Port configurations
   - IP addressing

2. **Change Control**
   - Configuration backups
   - Change tracking
   - Testing procedures

## Interview Tips
- Understand VLAN concepts and benefits
- Know configuration commands
- Explain inter-VLAN routing
- Understand security implications
- Be familiar with:
  - Troubleshooting methods
  - Best practices
  - Common issues
  - Design considerations
- Real-world examples:
  - Enterprise segmentation
  - Voice/Data separation
  - Security implementation
- Performance considerations:
  - Broadcast domain sizing
  - Trunk utilization
  - Routing efficiency 