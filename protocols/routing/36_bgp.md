# BGP (Border Gateway Protocol)

## Overview
BGP is a standardized exterior gateway protocol designed to exchange routing and reachability information among autonomous systems (AS) on the Internet. It is the protocol that makes the Internet work, handling inter-domain routing between different organizations and service providers.

## Technical Details

### Protocol Characteristics
- Layer: 4 (Transport)
- Protocol: TCP (Port 179)
- Current Version: BGP-4 (RFC 4271)
- Administrative Distance: 20 (eBGP), 200 (iBGP)
- Path Vector Protocol
- Incremental Updates

### BGP Features

1. **Session Types**
   - External BGP (eBGP)
   - Internal BGP (iBGP)
   - Route Reflector
   - Confederation

2. **Message Types**
   - OPEN
   - UPDATE
   - KEEPALIVE
   - NOTIFICATION
   - ROUTE-REFRESH

3. **Path Attributes**
   - ORIGIN
   - AS_PATH
   - NEXT_HOP
   - LOCAL_PREF
   - MED
   - COMMUNITY
   - ATOMIC_AGGREGATE
   - AGGREGATOR

4. **Route Selection Process**
   1. Highest WEIGHT
   2. Highest LOCAL_PREF
   3. Locally originated
   4. Shortest AS_PATH
   5. Lowest origin type
   6. Lowest MED
   7. eBGP over iBGP
   8. Lowest IGP metric
   9. Oldest path
   10. Lowest Router ID

### BGP Operation

1. **Neighbor Establishment**
   - TCP connection
   - OPEN message
   - Capability negotiation
   - Keepalive exchange

2. **Route Exchange**
   - Initial update
   - Incremental updates
   - Route refresh
   - Soft reconfiguration

3. **Route Selection**
   - Path attribute evaluation
   - Best path selection
   - Route installation
   - Route advertisement

## Implementation

### Basic Configuration

1. **Enable BGP**
   ```
   ! Configure BGP process
   Router(config)# router bgp 65000
   Router(config-router)# bgp router-id 1.1.1.1
   
   ! Configure neighbors
   Router(config-router)# neighbor 192.168.1.1 remote-as 65001
   Router(config-router)# neighbor 192.168.1.1 description PEER-1
   
   ! Configure networks
   Router(config-router)# network 172.16.0.0 mask 255.255.0.0
   ```

2. **iBGP Configuration**
   ```
   ! Configure route reflector
   Router(config-router)# neighbor 10.0.0.1 remote-as 65000
   Router(config-router)# neighbor 10.0.0.1 route-reflector-client
   
   ! Configure update source
   Router(config-router)# neighbor 10.0.0.1 update-source Loopback0
   ```

### Advanced Configuration

1. **Route Policy**
   ```
   ! Configure route-map
   Router(config)# route-map PEER-IN permit 10
   Router(config-route-map)# set local-preference 200
   
   ! Apply policy
   Router(config-router)# neighbor 192.168.1.1 route-map PEER-IN in
   ```

2. **Communities**
   ```
   ! Configure community lists
   Router(config)# ip community-list 1 permit 65000:100
   
   ! Set communities
   Router(config-route-map)# set community 65000:200
   ```

## Design Considerations

### Network Planning
1. **AS Design**
   - AS number selection
   - iBGP mesh
   - Route reflectors
   - Confederations

2. **Policy Planning**
   - Route filtering
   - Path manipulation
   - Community usage
   - Aggregation points

3. **Scalability**
   - Peer groups
   - Route reflectors
   - Update groups
   - Prefix lists

### High Availability
1. **Redundancy Planning**
   - Multiple peers
   - Multiple paths
   - BFD support
   - Graceful restart

2. **Performance Optimization**
   - Route dampening
   - Peer groups
   - Update groups
   - Soft reconfiguration

## Troubleshooting

### Common Issues
1. **Neighbor Problems**
   - TCP connection
   - Authentication
   - AS number mismatch
   - Update source

2. **Route Problems**
   - Missing routes
   - Route filtering
   - Path selection
   - Next-hop reachability

3. **Policy Issues**
   - Route-map logic
   - Community filtering
   - AS-path filtering
   - Prefix filtering

### Verification Commands
```
show ip bgp summary
show ip bgp neighbors
show ip bgp
show ip bgp paths
show ip bgp community
debug ip bgp updates
debug ip bgp events
debug ip bgp keepalives
```

## Best Practices

### Design Guidelines
1. **Peer Configuration**
   ```
   ! Configure peer groups
   Router(config-router)# neighbor PEER-GROUP peer-group
   Router(config-router)# neighbor PEER-GROUP remote-as 65001
   Router(config-router)# neighbor 192.168.1.1 peer-group PEER-GROUP
   
   ! Configure route reflector
   Router(config-router)# bgp cluster-id 1
   Router(config-router)# neighbor 10.0.0.1 route-reflector-client
   ```

2. **Policy Configuration**
   ```
   ! Configure prefix lists
   Router(config)# ip prefix-list FILTER seq 5 permit 172.16.0.0/16
   
   ! Configure AS path access lists
   Router(config)# ip as-path access-list 1 permit ^65000$
   ```

### Operational Procedures
1. **Monitoring**
   - Neighbor status
   - Route stability
   - Memory usage
   - CPU utilization

2. **Documentation**
   - AS relationships
   - Peer groups
   - Policies
   - Communities

## Security Considerations

### BGP Security
1. **Authentication**
   ```
   ! Configure MD5 authentication
   Router(config-router)# neighbor 192.168.1.1 password SECRET
   
   ! Configure TTL security
   Router(config-router)# neighbor 192.168.1.1 ttl-security hops 1
   ```

2. **Route Filtering**
   ```
   ! Configure prefix list
   Router(config)# ip prefix-list SECURE deny 10.0.0.0/8
   Router(config)# ip prefix-list SECURE permit 0.0.0.0/0 le 24
   
   ! Apply filter
   Router(config-router)# neighbor 192.168.1.1 prefix-list SECURE in
   ```

## Interview Tips
- Understand BGP operation
- Know path attributes
- Explain route selection
- Understand scaling techniques
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Service provider networks
  - Enterprise networks
  - Internet routing
  - Data center interconnect
- Best practices:
  - Route reflection
  - Policy design
  - Security measures
  - Monitoring setup
- Advanced concepts:
  - MP-BGP
  - VPNv4/VPNv6
  - RPKI
  - BGP flowspec
- Comparison with other protocols:
  - IGP differences
  - Policy control
  - Scalability aspects
  - Security considerations 