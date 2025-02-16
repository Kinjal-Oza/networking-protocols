# Static Routing

## Overview
Static routing is a method of network routing where routes are manually configured by network administrators, rather than being learned through dynamic routing protocols.

## Technical Details

### Route Characteristics
- Manually configured
- No protocol overhead
- No route updates
- Predictable path selection
- Administrative distance: 1 (most preferred)

### Route Types

1. **Standard Static Route**
   - Next-hop IP address
   - Exit interface
   - Fully specified path

2. **Default Route**
   - 0.0.0.0/0 (IPv4)
   - ::/0 (IPv6)
   - Catch-all route

3. **Floating Static Route**
   - Backup route
   - Higher administrative distance
   - Failover capability

## Implementation

### Basic Configuration

1. **IPv4 Static Routes**
   ```
   ! Next-hop IP
   Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1
   
   ! Exit interface
   Router(config)# ip route 192.168.2.0 255.255.255.0 GigabitEthernet0/1
   
   ! Default route
   Router(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1
   ```

2. **IPv6 Static Routes**
   ```
   ! Next-hop IPv6
   Router(config)# ipv6 route 2001:db8::/64 2001:db8:0:1::1
   
   ! Exit interface
   Router(config)# ipv6 route 2001:db8:1::/64 GigabitEthernet0/1
   
   ! Default route
   Router(config)# ipv6 route ::/0 2001:db8:0:1::1
   ```

### Advanced Configuration

1. **Floating Static Route**
   ```
   ! Primary route
   Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1
   
   ! Backup route with higher AD
   Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.2 150
   ```

2. **Permanent Static Route**
   ```
   ! Route remains even if interface goes down
   Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1 permanent
   ```

## Design Considerations

### Network Planning
1. **Route Selection**
   - Network topology
   - Bandwidth requirements
   - Redundancy needs
   - Security considerations

2. **Scalability**
   - Network growth
   - Management overhead
   - Documentation needs
   - Change control

3. **Performance**
   - Path optimization
   - Load distribution
   - Failover requirements

### High Availability
1. **Redundant Paths**
   - Multiple routes
   - Floating static routes
   - Administrative distance

2. **Failover Planning**
   - Backup routes
   - Recovery procedures
   - Monitoring requirements

## Troubleshooting

### Common Issues
1. **Reachability Problems**
   - Next-hop unreachable
   - Interface down
   - Recursive routing

2. **Route Selection**
   - Administrative distance
   - Metric conflicts
   - Path selection

3. **Performance Issues**
   - Suboptimal paths
   - Load distribution
   - Convergence time

### Verification Commands
```
show ip route static
show ip route summary
show running-config | section ip route
show ipv6 route static
ping
traceroute
```

## Best Practices

### Design Guidelines
1. **Route Planning**
   - Hierarchical design
   - Summarization
   - Default routes
   - Documentation

2. **Security**
   - Route filtering
   - Path protection
   - Access control

3. **Management**
   - Change control
   - Documentation
   - Monitoring

### Implementation Strategy
1. **Deployment**
   - Phased implementation
   - Testing procedures
   - Rollback plans

2. **Documentation**
   - Network diagram
   - Route table
   - Change history

3. **Monitoring**
   - Path verification
   - Performance metrics
   - Alert configuration

## Security Considerations

### Route Protection
1. **Access Control**
   ```
   ! Route filtering
   Router(config)# ip prefix-list ALLOWED permit 192.168.1.0/24
   
   ! Apply filter
   Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1 prefix-list ALLOWED
   ```

2. **Path Security**
   ```
   ! Secure routing path
   Router(config)# ip route 192.168.1.0 255.255.255.0 Tunnel0
   ```

## Integration

### Related Features
1. **Policy Routing**
   ```
   ! Route map configuration
   Router(config)# route-map POLICY permit 10
   Router(config-route-map)# match ip address 1
   Router(config-route-map)# set ip next-hop 10.0.0.1
   ```

2. **VRF Integration**
   ```
   ! VRF static route
   Router(config)# ip route vrf CUSTOMER1 192.168.1.0 255.255.255.0 10.0.0.1
   ```

## Interview Tips
- Understand static routing concepts
- Know configuration syntax
- Explain route selection process
- Understand high availability
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design considerations
  - Security implications
- Real-world scenarios:
  - Enterprise routing
  - Data center connectivity
  - Branch office deployment
- Best practices:
  - Route planning
  - Documentation
  - Change management
- Integration knowledge:
  - Policy routing
  - VRF implementation
  - Security features 