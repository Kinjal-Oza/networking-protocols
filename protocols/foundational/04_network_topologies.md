# Network Topologies

## Overview
Network topology refers to the arrangement of network elements (links, nodes) in a network. It encompasses both physical (actual layout) and logical (how data flows) arrangements of network components, each with its own advantages and use cases.

## Technical Details

### Physical Topologies

1. **Bus Topology**
   - Single central cable
   - All devices connect to main cable
   - Simple implementation
   - Limited scalability
   - Vulnerable to cable failures

2. **Star Topology**
   - Central hub/switch
   - Point-to-point connections
   - Easy troubleshooting
   - Scalable design
   - Common in LANs

3. **Ring Topology**
   - Devices form a circle
   - Each device connects to two others
   - Token passing possible
   - Resilient with dual rings
   - Used in FDDI, Token Ring

4. **Mesh Topology**
   - Full or partial mesh
   - Multiple paths between nodes
   - High redundancy
   - Complex implementation
   - Used in WANs/backbones

5. **Tree Topology**
   - Hierarchical design
   - Branch structure
   - Scalable architecture
   - Central management
   - Common in enterprise

### Logical Topologies

1. **Broadcast**
   - One-to-all communication
   - Ethernet networks
   - Simple implementation
   - Network segmentation
   - VLAN support

2. **Point-to-Point**
   - Direct connections
   - Dedicated bandwidth
   - Secure communication
   - WAN links
   - VPN tunnels

3. **Point-to-Multipoint**
   - One source, multiple destinations
   - Wireless networks
   - Satellite communications
   - MPLS VPNs
   - Multicast delivery

## Implementation

### Basic Design

1. **LAN Implementation**
   ```
   ! Configure switch ports
   Switch(config)# interface range GigabitEthernet0/1-24
   Switch(config-if-range)# switchport mode access
   Switch(config-if-range)# spanning-tree portfast
   
   ! Configure uplink
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# switchport mode trunk
   Switch(config-if)# switchport trunk allowed vlan all
   ```

2. **WAN Implementation**
   ```
   ! Configure point-to-point link
   Router(config)# interface Serial0/0
   Router(config-if)# ip address 192.168.1.1 255.255.255.252
   Router(config-if)# clock rate 64000
   
   ! Configure mesh link
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip address 10.0.1.1 255.255.255.0
   Router(config-if)# ip ospf network point-to-multipoint
   ```

### Advanced Design

1. **High Availability**
   ```
   ! Configure redundant links
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# channel-group 1 mode active
   
   Router(config)# interface Port-channel1
   Router(config-if)# switchport mode trunk
   
   ! Configure HSRP
   Router(config-if)# standby 1 ip 192.168.1.1
   Router(config-if)# standby 1 priority 110
   Router(config-if)# standby 1 preempt
   ```

2. **Traffic Engineering**
   ```
   ! Configure QoS
   Router(config)# class-map match-all VOICE
   Router(config-cmap)# match ip dscp ef
   
   Router(config)# policy-map QOS-POLICY
   Router(config-pmap)# class VOICE
   Router(config-pmap-c)# priority 512
   ```

## Design Considerations

### Network Planning
1. **Topology Selection**
   - Business requirements
   - Redundancy needs
   - Scalability requirements
   - Cost constraints
   - Management complexity

2. **Performance Requirements**
   - Bandwidth needs
   - Latency requirements
   - Traffic patterns
   - Growth projections
   - Application demands

3. **Reliability Planning**
   - Redundant paths
   - Failover mechanisms
   - Backup links
   - Power redundancy
   - Disaster recovery

### High Availability
1. **Link Redundancy**
   - Multiple uplinks
   - Load balancing
   - Link aggregation
   - Backup paths
   - Fast convergence

2. **Node Redundancy**
   - Redundant devices
   - Clustering
   - Virtual chassis
   - Stacking
   - Hot standby

## Troubleshooting

### Common Issues
1. **Physical Problems**
   - Cable failures
   - Port errors
   - Hardware failures
   - Power issues
   - Environmental factors

2. **Logical Problems**
   - Routing loops
   - Spanning tree issues
   - VLAN problems
   - Protocol conflicts
   - Bandwidth bottlenecks

### Verification Commands
```
show interfaces status
show spanning-tree
show etherchannel summary
show ip ospf neighbor
show cdp neighbors
show lldp neighbors
show power inline
```

## Best Practices

### Design Guidelines
1. **Physical Design**
   - Structured cabling
   - Cable management
   - Power planning
   - Environmental control
   - Physical security

2. **Logical Design**
   - VLAN segmentation
   - Route summarization
   - Protocol selection
   - Security zones
   - Management access

### Security Considerations
1. **Physical Security**
   ```
   ! Configure port security
   Switch(config-if)# switchport port-security
   Switch(config-if)# switchport port-security maximum 2
   Switch(config-if)# switchport port-security violation shutdown
   
   ! Configure storm control
   Switch(config-if)# storm-control broadcast level 20
   ```

2. **Logical Security**
   ```
   ! Configure access lists
   Router(config)# ip access-list extended PROTECT-MGMT
   Router(config-ext-nacl)# permit tcp host 192.168.1.100 any eq 22
   Router(config-ext-nacl)# deny ip any any log
   
   ! Apply to interface
   Router(config-if)# ip access-group PROTECT-MGMT in
   ```

## Interview Tips
- Understand topology types
- Know advantages/disadvantages
- Explain redundancy concepts
- Understand scalability
- Be familiar with:
  - Design principles
  - Implementation methods
  - Troubleshooting approaches
  - Security considerations
- Real-world scenarios:
  - Campus networks
  - Data centers
  - Branch offices
  - WAN connectivity
- Best practices:
  - Redundancy planning
  - Performance optimization
  - Security implementation
  - Growth management
- Advanced concepts:
  - SDN integration
  - Network virtualization
  - Cloud connectivity
  - Hybrid topologies 