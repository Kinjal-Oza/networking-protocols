# EIGRP (Enhanced Interior Gateway Routing Protocol)

## Overview
EIGRP is a Cisco-proprietary advanced distance-vector routing protocol that combines features of both link-state and distance-vector protocols. It provides fast convergence, loop prevention, and efficient bandwidth usage through its Diffusing Update Algorithm (DUAL).

## Technical Details

### Protocol Characteristics
- Advanced Distance Vector Protocol
- Protocol Number: 88
- Administrative Distance: 90 (Internal), 170 (External)
- Multicast Address: 224.0.0.10
- K-values for metric calculation
- Support for IPv4 and IPv6

### EIGRP Components

1. **Protocol Tables**
   - Neighbor Table: Adjacent routers
   - Topology Table: All routes learned
   - Routing Table: Best paths only

2. **Packet Types**
   - Hello: Neighbor discovery/maintenance
   - Update: Route information
   - Query: Route information request
   - Reply: Response to query
   - ACK: Reliable delivery confirmation

3. **DUAL Algorithm Components**
   - Feasible Distance (FD)
   - Reported Distance (RD)
   - Feasible Successor (FS)
   - Successor Route
   - Feasibility Condition

## Implementation

### Basic Configuration

1. **Enable EIGRP**
   ```
   ! Configure EIGRP process
   Router(config)# router eigrp 100
   
   ! Configure networks
   Router(config-router)# network 192.168.1.0 0.0.0.255
   Router(config-router)# network 10.0.0.0 0.255.255.255
   
   ! Configure router ID
   Router(config-router)# eigrp router-id 1.1.1.1
   ```

2. **Interface Configuration**
   ```
   ! Configure interface parameters
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# bandwidth 1000000
   Router(config-if)# delay 10
   Router(config-if)# no ip split-horizon eigrp 100
   ```

### Advanced Configuration

1. **Named Mode Configuration**
   ```
   ! Configure named EIGRP
   Router(config)# router eigrp ENTERPRISE
   Router(config-router)# address-family ipv4 autonomous-system 100
   Router(config-router-af)# network 192.168.1.0 0.0.0.255
   ```

2. **Route Summarization**
   ```
   ! Configure manual summary
   Router(config-if)# ip summary-address eigrp 100 192.168.0.0 255.255.0.0
   
   ! Configure auto-summary
   Router(config-router)# auto-summary
   ```

3. **Authentication**
   ```
   ! Configure MD5 authentication
   Router(config-if)# ip authentication mode eigrp 100 md5
   Router(config-if)# ip authentication key-chain eigrp 100 EIGRP-KEY
   
   ! Configure key chain
   Router(config)# key chain EIGRP-KEY
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECURE_PWD
   ```

## Design Considerations

### Network Planning
1. **Topology Design**
   - Hierarchical structure
   - Summarization boundaries
   - Query boundaries
   - Stub networks

2. **Scalability**
   - Number of neighbors
   - Route table size
   - Query domain size
   - Memory requirements

3. **Performance**
   - Bandwidth utilization
   - CPU usage
   - Convergence time
   - Query processing

### High Availability
1. **Redundancy**
   - Multiple paths
   - Feasible successors
   - Backup routes
   - Fast convergence

2. **Load Balancing**
   - Unequal cost load balancing
   - Variance configuration
   - Traffic distribution
   - Path selection

## Troubleshooting

### Common Issues
1. **Neighbor Formation**
   - K-value mismatch
   - Authentication failure
   - AS number mismatch
   - ACL blocking

2. **Route Problems**
   - Stuck in Active (SIA)
   - Missing routes
   - Suboptimal paths
   - Query storms

3. **Performance Issues**
   - High CPU usage
   - Excessive queries
   - Slow convergence
   - Bandwidth saturation

### Verification Commands
```
show ip eigrp neighbors
show ip eigrp topology
show ip eigrp interfaces
show ip route eigrp
debug eigrp packets
debug eigrp neighbors
```

## Best Practices

### Design Guidelines
1. **Network Design**
   - Clear hierarchy
   - Summarization points
   - Query boundaries
   - Stub configuration

2. **Performance Tuning**
   - Hello/Hold timers
   - Bandwidth configuration
   - Split horizon
   - Route filtering

3. **Security Measures**
   - Authentication
   - Route filtering
   - Neighbor filtering
   - Stub router configuration

### Operational Procedures
1. **Maintenance**
   - Configuration backup
   - Software updates
   - Performance monitoring
   - Documentation

2. **Monitoring**
   - Neighbor stability
   - Route stability
   - Query activity
   - Resource utilization

## Security Considerations

### Protocol Security
1. **Authentication**
   ```
   ! Configure authentication
   Router(config)# key chain EIGRP-KEY
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECURE_PWD
   Router(config-keychain-key)# accept-lifetime 00:00:00 Jan 1 2024 infinite
   Router(config-keychain-key)# send-lifetime 00:00:00 Jan 1 2024 infinite
   ```

2. **Route Filtering**
   ```
   ! Configure distribute list
   Router(config)# ip prefix-list FILTER permit 192.168.0.0/16
   Router(config-router)# distribute-list prefix FILTER in
   ```

## Interview Tips
- Understand DUAL algorithm
- Know neighbor requirements
- Explain metric calculation
- Understand query process
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Enterprise networks
  - Campus networks
  - WAN deployment
- Best practices:
  - Summarization
  - Query domain control
  - Authentication
  - Performance tuning
- Advanced concepts:
  - Named mode configuration
  - IPv6 implementation
  - Route redistribution
  - Stub networks 