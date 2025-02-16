# EIGRP (Enhanced Interior Gateway Routing Protocol)

## Overview
EIGRP is a Cisco proprietary advanced distance-vector routing protocol that provides superior convergence properties and operating efficiency compared to traditional routing protocols. It combines the advantages of link-state protocols with those of distance-vector protocols.

## Technical Details

### Protocol Characteristics
- Layer: 3 (Network)
- Cisco Proprietary
- Protocol Number: 88
- Multicast Address: 224.0.0.10
- Administrative Distance: 90 (Internal), 170 (External)
- Composite Metric Components:
  - Bandwidth
  - Delay
  - Reliability
  - Load
  - MTU

### EIGRP Features

1. **DUAL (Diffusing Update Algorithm)**
   - Loop prevention
   - Rapid convergence
   - Backup path maintenance
   - Topology table management

2. **Packet Types**
   - Hello
   - Update
   - Query
   - Reply
   - ACK
   - SIA-Query
   - SIA-Reply

3. **Tables**
   - Neighbor Table
   - Topology Table
   - Routing Table

4. **Route Types**
   - Internal
   - External
   - Summary
   - Default

### EIGRP Operation

1. **Neighbor Discovery**
   - Hello packets
   - Hold timer
   - Authentication
   - K-values matching

2. **Route Exchange**
   - Initial full update
   - Incremental updates
   - Reliable transport
   - Bounded updates

3. **Path Selection**
   - Feasible distance
   - Reported distance
   - Feasibility condition
   - Successor/Feasible successor

## Implementation

### Basic Configuration

1. **Enable EIGRP**
   ```
   ! Enable EIGRP for AS 100
   Router(config)# router eigrp 100
   Router(config-router)# network 192.168.1.0 0.0.0.255
   Router(config-router)# no auto-summary
   
   ! Configure interface
   Router(config)# interface GigabitEthernet0/0
   Router(config-if)# ip hello-interval eigrp 100 5
   Router(config-if)# ip hold-time eigrp 100 15
   ```

2. **Metric Configuration**
   ```
   ! Configure K-values
   Router(config-router)# metric weights 0 1 0 1 0 0
   
   ! Configure bandwidth
   Router(config-if)# bandwidth 1000000
   Router(config-if)# delay 10
   ```

### Advanced Configuration

1. **Authentication**
   ```
   ! Configure MD5 authentication
   Router(config)# key chain EIGRP-KEY
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECRET
   
   ! Apply to interface
   Router(config-if)# ip authentication mode eigrp 100 md5
   Router(config-if)# ip authentication key-chain eigrp 100 EIGRP-KEY
   ```

2. **Route Summarization**
   ```
   ! Configure manual summary
   Router(config-if)# ip summary-address eigrp 100 192.168.0.0 255.255.0.0
   
   ! Configure automatic summary
   Router(config-router)# auto-summary
   ```

## Design Considerations

### Network Planning
1. **AS Design**
   - AS number selection
   - Network boundaries
   - Route filtering
   - Summarization points

2. **Metric Planning**
   - Bandwidth settings
   - Delay settings
   - K-value selection
   - Path selection

3. **Scalability**
   - Query boundaries
   - Stub configuration
   - Route filtering
   - Summarization

### High Availability
1. **Redundancy Planning**
   - Multiple paths
   - Feasible successors
   - Load sharing
   - Fast convergence

2. **Performance Optimization**
   - Query scope
   - Update filtering
   - Stub routing
   - Graceful shutdown

## Troubleshooting

### Common Issues
1. **Neighbor Problems**
   - K-value mismatch
   - Authentication failure
   - Hello/Hold timer mismatch
   - AS number mismatch

2. **Route Problems**
   - Missing routes
   - Suboptimal paths
   - Route flapping
   - SIA routes

3. **Performance Issues**
   - High CPU usage
   - Memory constraints
   - Convergence delays
   - Query storms

### Verification Commands
```
show ip eigrp neighbors
show ip eigrp topology
show ip eigrp interfaces
show ip route eigrp
show ip protocols
debug eigrp packets
debug eigrp neighbors
```

## Best Practices

### Design Guidelines
1. **Network Design**
   ```
   ! Configure stub router
   Router(config-router)# eigrp stub connected summary
   
   ! Configure route filtering
   Router(config)# ip prefix-list FILTER permit 192.168.0.0/16
   Router(config-router)# distribute-list prefix FILTER in
   ```

2. **Security Configuration**
   ```
   ! Configure authentication
   Router(config-if)# ip authentication mode eigrp 100 md5
   Router(config-if)# ip authentication key-chain eigrp 100 EIGRP-KEY
   ```

### Operational Procedures
1. **Monitoring**
   - Neighbor status
   - Route stability
   - CPU utilization
   - Memory usage

2. **Documentation**
   - AS numbers
   - Network ranges
   - Neighbor relationships
   - Authentication keys

## Security Considerations

### EIGRP Security
1. **Authentication**
   ```
   ! Configure key chain
   Router(config)# key chain EIGRP-KEY
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string COMPLEX_PASS
   Router(config-keychain-key)# accept-lifetime 00:00:00 Jan 1 2024 infinite
   Router(config-keychain-key)# send-lifetime 00:00:00 Jan 1 2024 infinite
   ```

2. **Route Filtering**
   ```
   ! Configure route filtering
   Router(config)# ip prefix-list SECURE deny 10.0.0.0/8
   Router(config)# ip prefix-list SECURE permit 0.0.0.0/0 le 32
   Router(config-router)# distribute-list prefix SECURE in
   ```

## Interview Tips
- Understand EIGRP operation
- Know DUAL algorithm
- Explain route selection
- Understand packet types
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Enterprise networks
  - Campus networks
  - WAN deployment
  - Branch offices
- Best practices:
  - Stub configuration
  - Authentication
  - Route filtering
  - Summarization
- Advanced concepts:
  - Named mode configuration
  - IPv6 support
  - VRF awareness
  - DMVPN integration
- Comparison with other protocols:
  - OSPF differences
  - IS-IS differences
  - BGP differences
  - Migration strategies