# LISP (Locator/ID Separation Protocol)

## Overview
LISP is a routing architecture that separates location and identity of network endpoints, enabling more efficient routing and addressing in the Internet. It's designed to support mobility, multi-homing, and improved scalability of the Internet routing system.

## Technical Details

### Protocol Characteristics
- Separation of location and identity
- Map-and-encap architecture
- Control plane/data plane separation
- Support for IPv4 and IPv6
- Mobility support
- Multi-homing capabilities

### LISP Components

1. **Network Elements**
   - ITR (Ingress Tunnel Router)
   - ETR (Egress Tunnel Router)
   - xTR (Combined ITR/ETR)
   - Map Server
   - Map Resolver
   - PITR (Proxy ITR)
   - PETR (Proxy ETR)

2. **Address Types**
   - EID (Endpoint Identifier)
   - RLOC (Routing Locator)
   - Instance ID
   - VRF

3. **Control Messages**
   - Map-Request
   - Map-Reply
   - Map-Register
   - Map-Notify
   - Info-Request
   - Info-Reply

## Implementation

### Basic Configuration

1. **Enable LISP**
   ```
   ! Enable LISP routing
   Router(config)# router lisp
   
   ! Configure instance ID
   Router(config-router-lisp)# instance-id 0
   
   ! Configure EID space
   Router(config-router-lisp-instance)# database-mapping 192.168.1.0/24 10.0.0.1 priority 1 weight 100
   ```

2. **Map Server Configuration**
   ```
   ! Configure Map Server
   Router(config-router-lisp)# site SITE1
   Router(config-router-lisp-site)# authentication-key SECURE_KEY
   Router(config-router-lisp-site)# eid-prefix 192.168.1.0/24
   ```

### Advanced Configuration

1. **Mobility Configuration**
   ```
   ! Configure mobility
   Router(config-router-lisp)# mobility-first-hop router
   Router(config-router-lisp)# mobility host dynamic
   
   ! Configure dynamic EID
   Router(config-router-lisp)# dynamic-eid MOBILE-EID
   Router(config-router-lisp-dynamic-eid)# database-mapping 192.168.1.0/24 10.0.0.1
   ```

2. **Multi-homing Setup**
   ```
   ! Configure multiple RLOCs
   Router(config-router-lisp-instance)# database-mapping 192.168.1.0/24 10.0.0.1 priority 1 weight 50
   Router(config-router-lisp-instance)# database-mapping 192.168.1.0/24 10.0.0.2 priority 1 weight 50
   ```

3. **Proxy Configuration**
   ```
   ! Configure PITR
   Router(config-router-lisp)# proxy-itr 10.0.0.1
   
   ! Configure PETR
   Router(config-router-lisp)# proxy-etr
   Router(config-router-lisp)# use-petr 10.0.0.2
   ```

## Design Considerations

### Network Planning
1. **Architecture Design**
   - EID space allocation
   - RLOC assignment
   - Instance planning
   - Mobility requirements

2. **Scalability**
   - Number of EIDs
   - Mapping database size
   - Cache requirements
   - Control plane load

3. **Performance**
   - Mapping latency
   - Encapsulation overhead
   - Cache efficiency
   - Path optimization

### High Availability
1. **Redundancy**
   - Multiple RLOCs
   - Map Server redundancy
   - Proxy redundancy
   - Failover mechanisms

2. **Load Balancing**
   - Priority/weight configuration
   - Traffic engineering
   - Path selection
   - RLOC selection

## Troubleshooting

### Common Issues
1. **Mapping Problems**
   - Registration failures
   - Resolution delays
   - Cache misses
   - Authentication issues

2. **Encapsulation Issues**
   - MTU problems
   - Fragment handling
   - Path MTU discovery
   - Encapsulation errors

3. **Control Plane Problems**
   - Map Server connectivity
   - Registration timeouts
   - Database synchronization
   - Version mismatches

### Verification Commands
```
show lisp
show lisp site
show lisp map-cache
show lisp database
debug lisp control-plane
debug lisp data-plane
show ip cef
show lisp instance-id
```

## Best Practices

### Design Guidelines
1. **EID Space Planning**
   - Address allocation
   - Summarization
   - Instance isolation
   - Mobility zones

2. **RLOC Architecture**
   - Core stability
   - Path redundancy
   - Load distribution
   - Proxy placement

3. **Security Measures**
   - Authentication
   - EID filtering
   - Control plane protection
   - Data plane security

### Operational Procedures
1. **Maintenance**
   - Configuration backup
   - Database maintenance
   - Performance monitoring
   - Documentation

2. **Monitoring**
   - Mapping status
   - Cache utilization
   - Control messages
   - Traffic patterns

## Security Considerations

### Protocol Security
1. **Authentication**
   ```
   ! Configure site authentication
   Router(config-router-lisp-site)# authentication-key SECURE_KEY
   Router(config-router-lisp-site)# authentication-type sha2
   ```

2. **EID Security**
   ```
   ! Configure EID filtering
   Router(config-router-lisp)# map-request-source 10.0.0.1
   Router(config-router-lisp)# allowed-locator-set LOC-SET1
   ```

## Interview Tips
- Understand LISP architecture
- Know EID/RLOC concepts
- Explain mapping system
- Understand mobility features
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Data center mobility
  - Multi-homing
  - Network virtualization
  - Cloud migration
- Best practices:
  - Address planning
  - Mapping system design
  - Security implementation
  - Performance optimization
- Advanced concepts:
  - LISP mobility
  - Multi-instance
  - Traffic engineering
  - Proxy functions 