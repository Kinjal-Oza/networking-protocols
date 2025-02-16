# IS-IS (Intermediate System to Intermediate System)

## Overview
IS-IS is a link-state routing protocol designed for large-scale networks, particularly service provider networks. It's unique in operating directly over Layer 2 rather than IP, and uses a hierarchical routing domain structure with support for both IPv4 and IPv6.

## Technical Details

### Protocol Characteristics
- Link-state Protocol
- OSI Layer 2 Protocol (no IP required)
- Administrative Distance: 115
- Support for IPv4 and IPv6
- Hierarchical routing with areas
- CLNS-based addressing

### IS-IS Components

1. **Network Types**
   - Level 1 (L1): Intra-area routing
   - Level 2 (L2): Inter-area routing
   - Level 1-2: Both levels
   - Point-to-Point
   - Broadcast

2. **Packet Types**
   - Hello (IIH)
     - Level 1 LAN
     - Level 2 LAN
     - Point-to-Point
   - LSP (Link State PDU)
   - CSNP (Complete Sequence Number PDU)
   - PSNP (Partial Sequence Number PDU)

3. **Addressing**
   - NET (Network Entity Title)
   - Area ID
   - System ID
   - NSEL (N-Selector)
   - Format: Area.System-ID.NSEL

## Implementation

### Basic Configuration

1. **Enable IS-IS**
   ```
   ! Configure IS-IS process
   Router(config)# router isis PROCESS-NAME
   Router(config-router)# net 49.0001.1111.1111.1111.00
   
   ! Enable on interface
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip router isis PROCESS-NAME
   ```

2. **Level Configuration**
   ```
   ! Configure IS-IS level
   Router(config-router)# is-type level-2-only
   
   ! Interface level configuration
   Router(config-if)# isis circuit-type level-2-only
   ```

### Advanced Configuration

1. **Authentication**
   ```
   ! Configure area authentication
   Router(config-router)# area-password SECURE_PWD
   
   ! Configure interface authentication
   Router(config-if)# isis authentication mode md5
   Router(config-if)# isis authentication key-chain ISIS-KEY
   ```

2. **Metric Configuration**
   ```
   ! Configure wide metrics
   Router(config-router)# metric-style wide
   
   ! Configure interface metric
   Router(config-if)# isis metric 100 level-2
   ```

3. **IPv6 Support**
   ```
   ! Enable IPv6 routing
   Router(config-router)# address-family ipv6
   Router(config-router-af)# multi-topology
   
   ! Enable on interface
   Router(config-if)# ipv6 router isis PROCESS-NAME
   ```

## Design Considerations

### Network Planning
1. **Area Design**
   - Level 1 areas
   - Level 2 backbone
   - Area addressing
   - Hierarchy planning

2. **Scalability**
   - Number of routers
   - LSP size
   - Database size
   - Memory requirements

3. **Performance**
   - SPF calculation
   - LSP generation
   - Update frequency
   - Bandwidth usage

### High Availability
1. **Redundancy**
   - Multiple paths
   - Mesh topology
   - Backup routes
   - Fast convergence

2. **Load Balancing**
   - Equal cost paths
   - Traffic engineering
   - Path selection
   - Metric tuning

## Troubleshooting

### Common Issues
1. **Neighbor Formation**
   - NET mismatch
   - Authentication failure
   - MTU mismatch
   - Level mismatch

2. **Database Problems**
   - LSP corruption
   - Sequence numbers
   - Database overflow
   - Update issues

3. **Performance Issues**
   - High CPU usage
   - Memory exhaustion
   - Slow convergence
   - LSP storms

### Verification Commands
```
show isis neighbors
show isis database
show isis topology
show clns interface
show clns protocol
debug isis adj-packets
debug isis update-packets
```

## Best Practices

### Design Guidelines
1. **Area Planning**
   - Hierarchical design
   - Summarization
   - Backbone connectivity
   - Area size limits

2. **Performance Tuning**
   - Metric configuration
   - SPF timers
   - LSP generation
   - Update control

3. **Security Measures**
   - Authentication
   - Route filtering
   - Resource protection
   - Access control

### Operational Procedures
1. **Maintenance**
   - Regular backups
   - Version control
   - Change management
   - Documentation

2. **Monitoring**
   - Database status
   - Neighbor stability
   - Route stability
   - Resource usage

## Security Considerations

### Protocol Security
1. **Authentication**
   ```
   ! Configure key chain
   Router(config)# key chain ISIS-KEY
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECURE_PWD
   
   ! Apply authentication
   Router(config-if)# isis authentication mode md5
   Router(config-if)# isis authentication key-chain ISIS-KEY
   ```

2. **Route Filtering**
   ```
   ! Configure route filter
   Router(config)# route-map ISIS-FILTER permit 10
   Router(config-route-map)# match ip address prefix-list ALLOWED
   
   ! Apply filter
   Router(config-router)# distribute-list route-map ISIS-FILTER in
   ```

## Interview Tips
- Understand IS-IS architecture
- Know area levels and types
- Explain NET addressing
- Understand packet types
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Service provider networks
  - Large enterprise networks
  - Data center design
- Best practices:
  - Area planning
  - Authentication
  - Performance tuning
  - Migration strategies
- Advanced concepts:
  - Multi-topology routing
  - Traffic engineering
  - IPv6 integration
  - Database synchronization 