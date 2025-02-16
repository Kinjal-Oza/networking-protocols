# Network Topologies

## Overview
Network topology refers to the physical or logical arrangement of network devices in relation to one another. It defines how different nodes in a network are connected and communicate.

## Technical Details

### Physical Topologies

1. **Bus Topology**
   - Characteristics:
     - Single central cable (backbone)
     - All devices connect directly to backbone
     - Terminators at each end
   - Advantages:
     - Simple to implement
     - Cost-effective
     - Easy to extend
   - Disadvantages:
     - Single point of failure
     - Limited cable length
     - Performance degrades with heavy traffic

2. **Star Topology**
   - Characteristics:
     - Central hub/switch
     - Point-to-point connections
     - Most common in modern LANs
   - Advantages:
     - Easy to troubleshoot
     - Fault isolation
     - Easy to modify
   - Disadvantages:
     - Dependent on central device
     - More cabling required
     - Higher cost

3. **Ring Topology**
   - Characteristics:
     - Each device connects to exactly two other devices
     - Data travels in one direction
     - Token passing for access control
   - Advantages:
     - Deterministic performance
     - No collisions
     - Equal access
   - Disadvantages:
     - Single point of failure affects all
     - Complex to modify
     - Harder to troubleshoot

4. **Mesh Topology**
   - Types:
     - Full mesh (all-to-all connections)
     - Partial mesh (some-to-some connections)
   - Advantages:
     - High redundancy
     - Fault tolerant
     - Load balancing
   - Disadvantages:
     - Complex implementation
     - Expensive
     - Difficult to manage

5. **Tree/Hierarchical Topology**
   - Characteristics:
     - Branching structure
     - Multiple layers
     - Parent-child relationship
   - Advantages:
     - Scalable
     - Easy to manage
     - Segmentation support
   - Disadvantages:
     - Dependent on root
     - Complex cabling
     - Higher latency for lower branches

### Logical Topologies

1. **Broadcast**
   - All nodes can transmit to all other nodes
   - Used in Ethernet networks
   - Collision detection/avoidance important

2. **Token Passing**
   - Controlled access to medium
   - Used in Token Ring and FDDI
   - Deterministic performance

### Hybrid Topologies

1. **Star-Bus**
   - Combines star and bus topologies
   - Common in larger networks
   - Improved scalability

2. **Star-Ring**
   - Physical star, logical ring
   - Used in Token Ring networks
   - Better fault tolerance

## Implementation Considerations

### Physical Layer
- Cable types (Copper, Fiber)
- Distance limitations
- Environmental factors
- Cost constraints

### Data Link Layer
- Access methods
- Collision handling
- Frame forwarding
- Loop prevention

### Network Layer
- Routing protocols
- Address assignment
- Traffic patterns
- Redundancy

## Troubleshooting

### Common Issues
1. **Single Points of Failure**
   - Redundant links
   - Backup devices
   - Failover mechanisms

2. **Performance Bottlenecks**
   - Traffic analysis
   - Capacity planning
   - Load balancing

3. **Scalability Challenges**
   - Modular design
   - Hierarchical addressing
   - Growth planning

### Tools
- Network diagrams
- Cable testers
- Protocol analyzers
- Monitoring systems

## Interview Tips
- Understand pros/cons of each topology
- Know real-world applications
- Explain topology selection criteria
- Understand redundancy concepts
- Be familiar with troubleshooting approaches
- Know how topologies affect:
  - Performance
  - Reliability
  - Cost
  - Scalability
- Understand modern implementations:
  - Data centers
  - Cloud networks
  - Enterprise networks 