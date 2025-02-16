# PBR (Policy-Based Routing)

## Overview
Policy-Based Routing (PBR) is a technique that allows routing decisions to be made based on policies defined by network administrators, rather than the routing table. It provides a flexible mechanism to implement routing policies based on factors such as source/destination IP addresses, protocol types, and application types.

## Technical Details

### Characteristics
- Path selection based on policies
- Applied on interfaces
- Processes before routing table
- Local policy database
- Supports IPv4 and IPv6
- Multiple match criteria

### Policy Components

1. **Match Criteria**
   - Source/Destination IP
   - Protocol type
   - Packet length
   - Application ports
   - Input interface
   - Access lists

2. **Set Actions**
   - Next hop IP
   - Output interface
   - Default next hop
   - Default interface
   - IP precedence
   - QoS group

3. **Processing Order**
   - Local policy
   - Policy routing
   - Regular routing
   - Default route

## Implementation

### Basic Configuration

1. **Define Route Map**
   ```
   ! Configure route map
   Router(config)# route-map POLICY-ROUTE permit 10
   Router(config-route-map)# match ip address 101
   Router(config-route-map)# set ip next-hop 192.168.1.1
   
   ! Apply to interface
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip policy route-map POLICY-ROUTE
   ```

2. **Access List Configuration**
   ```
   ! Define matching criteria
   Router(config)# access-list 101 permit ip 10.0.0.0 0.255.255.255 any
   Router(config)# access-list 101 permit ip 172.16.0.0 0.15.255.255 any
   ```

### Advanced Configuration

1. **Multiple Next Hops**
   ```
   ! Configure multiple next hops
   Router(config)# route-map MULTI-PATH permit 10
   Router(config-route-map)# set ip next-hop 192.168.1.1 192.168.1.2
   Router(config-route-map)# set ip next-hop verify-availability
   ```

2. **Load Sharing**
   ```
   ! Configure load sharing
   Router(config)# route-map LOAD-SHARE permit 10
   Router(config-route-map)# match ip address 101
   Router(config-route-map)# set ip next-hop 192.168.1.1
   Router(config-route-map)# set ip next-hop 192.168.1.2
   ```

3. **QoS Integration**
   ```
   ! Configure QoS policy
   Router(config)# route-map QOS-POLICY permit 10
   Router(config-route-map)# match ip address 101
   Router(config-route-map)# set ip precedence priority
   Router(config-route-map)# set ip next-hop 192.168.1.1
   ```

## Design Considerations

### Network Planning
1. **Policy Design**
   - Traffic patterns
   - Application requirements
   - Network topology
   - Bandwidth allocation

2. **Scalability**
   - Number of policies
   - Processing overhead
   - Memory requirements
   - Update frequency

3. **Performance Impact**
   - CPU utilization
   - Packet processing
   - Latency considerations
   - Throughput impact

### High Availability
1. **Redundancy**
   - Multiple next hops
   - Failover paths
   - Backup routes
   - Fast recovery

2. **Load Distribution**
   - Traffic sharing
   - Path selection
   - Load balancing
   - Bandwidth utilization

## Troubleshooting

### Common Issues
1. **Policy Problems**
   - Incorrect matching
   - Set action failures
   - Order of operations
   - Route recursion

2. **Performance Issues**
   - High CPU usage
   - Packet drops
   - Latency increase
   - Memory exhaustion

3. **Configuration Problems**
   - Syntax errors
   - Missing criteria
   - Interface mismatch
   - Policy conflicts

### Verification Commands
```
show route-map
show ip policy
show ip local policy
debug ip policy
show ip cache policy
show ip policy statistics
```

## Best Practices

### Design Guidelines
1. **Policy Planning**
   - Clear objectives
   - Documented policies
   - Change control
   - Performance monitoring

2. **Implementation Strategy**
   - Phased deployment
   - Testing procedures
   - Rollback plans
   - Documentation

3. **Performance Optimization**
   - Efficient matching
   - Minimal policies
   - Regular cleanup
   - Resource monitoring

### Operational Procedures
1. **Maintenance**
   - Regular review
   - Policy updates
   - Performance tuning
   - Documentation

2. **Monitoring**
   - Policy statistics
   - Resource usage
   - Traffic patterns
   - Error logging

## Security Considerations

### Policy Security
1. **Access Control**
   ```
   ! Configure secure policy
   Router(config)# ip access-list extended SECURE-POLICY
   Router(config-ext-nacl)# permit ip 10.0.0.0 0.255.255.255 any
   Router(config-ext-nacl)# deny ip any any log
   ```

2. **Route Protection**
   ```
   ! Configure route protection
   Router(config)# route-map SECURE-ROUTE permit 10
   Router(config-route-map)# match ip address SECURE-POLICY
   Router(config-route-map)# set ip next-hop verify-availability
   ```

## Interview Tips
- Understand PBR concepts
- Know configuration syntax
- Explain policy processing
- Understand impact on performance
- Be familiar with:
  - Match criteria
  - Set actions
  - Troubleshooting methods
  - Design considerations
- Real-world scenarios:
  - Traffic engineering
  - Application routing
  - Load balancing
  - QoS integration
- Best practices:
  - Policy design
  - Implementation strategy
  - Performance optimization
  - Security integration
- Advanced concepts:
  - Policy inheritance
  - Route recursion
  - Next-hop verification
  - Policy statistics 