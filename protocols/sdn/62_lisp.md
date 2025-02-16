# LISP (Locator/ID Separation Protocol)

## Overview
LISP is a routing architecture that separates location and identity of network endpoints, enabling more efficient routing and addressing in the Internet. It uses a mapping system to bind Endpoint Identifiers (EIDs) to Routing Locators (RLOCs), facilitating mobility, multihoming, and network virtualization.

## Technical Details

### Protocol Characteristics
- Control/Data plane separation
- RFC 6830 standard
- UDP encapsulation (port 4341)
- Map-and-Encap approach
- Dynamic EID-to-RLOC mapping
- Site multihoming support
- Mobility enablement

### LISP Components

1. **Network Elements**
   - Ingress Tunnel Router (ITR)
   - Egress Tunnel Router (ETR)
   - Proxy ITR/ETR (PITR/PETR)
   - Map Server (MS)
   - Map Resolver (MR)
   - LISP Database
   - LISP Cache

2. **Address Types**
   - Endpoint Identifiers (EIDs)
   - Routing Locators (RLOCs)
   - Instance IDs
   - Virtual Network IDs
   - Site IDs

3. **Control Messages**
   - Map-Request
   - Map-Reply
   - Map-Register
   - Map-Notify
   - Info-Request/Reply

## Implementation

### Basic Configuration

1. **Enable LISP**
   ```
   ! Enable LISP routing
   Router(config)# router lisp
   Router(config-router-lisp)# instance-id 1
   Router(config-router-lisp-inst)# service ipv4
   
   ! Configure EID space
   Router(config-router-lisp-inst)# database-mapping 192.168.1.0/24 10.0.0.1 priority 1 weight 100
   Router(config-router-lisp-inst)# eid-table default
   ```

2. **Map Server Configuration**
   ```
   ! Configure Map Server
   Router(config)# router lisp
   Router(config-router-lisp)# site SITE1
   Router(config-router-lisp-site)# authentication-key SECRET
   Router(config-router-lisp-site)# eid-prefix 192.168.1.0/24
   Router(config-router-lisp-site)# exit
   Router(config-router-lisp)# map-server
   ```

### Advanced Configuration

1. **Mobility Configuration**
   ```
   ! Configure dynamic EID
   Router(config)# router lisp
   Router(config-router-lisp)# dynamic-eid MOBILE-EID
   Router(config-router-lisp-dyn-eid)# database-mapping 192.168.2.0/24 10.0.0.2
   Router(config-router-lisp-dyn-eid)# map-notify-group 239.0.0.1
   
   ! Apply to interface
   Router(config)# interface GigabitEthernet0/0
   Router(config-if)# lisp mobility MOBILE-EID
   ```

2. **Multihoming Setup**
   ```
   ! Configure multiple RLOCs
   Router(config)# router lisp
   Router(config-router-lisp)# database-mapping 192.168.1.0/24 10.0.0.1 priority 1 weight 50
   Router(config-router-lisp)# database-mapping 192.168.1.0/24 10.0.0.2 priority 1 weight 50
   
   ! Configure RLOC probing
   Router(config-router-lisp)# loc-reach-algorithm rloc-probing
   ```

## Design Considerations

### Architecture Planning
1. **Network Design**
   - RLOC addressing
   - EID addressing
   - Mapping system
   - Site placement
   - Scalability

2. **Service Integration**
   - Mobility services
   - Multihoming
   - Traffic engineering
   - Security services
   - IPv6 transition

3. **High Availability**
   - Map server redundancy
   - ETR redundancy
   - RLOC failure handling
   - Fast convergence
   - State synchronization

### Performance Optimization
1. **Control Plane**
   - Map cache sizing
   - Registration timers
   - Probe intervals
   - Authentication
   - State compression

2. **Data Plane**
   - MTU handling
   - Encapsulation overhead
   - Path optimization
   - Load balancing
   - Traffic prioritization

## Troubleshooting

### Common Issues
1. **Mapping Problems**
   - Registration failures
   - Map cache misses
   - Authentication issues
   - Timer mismatches
   - Database inconsistency

2. **Connectivity Issues**
   - RLOC reachability
   - Encapsulation failures
   - MTU problems
   - Path selection
   - Mobility events

3. **Integration Challenges**
   - NAT traversal
   - IPv6 compatibility
   - Multicast handling
   - Security policy
   - Route redistribution

### Verification Commands
```
show ip lisp
show lisp site
show lisp map-cache
show lisp database
show ip lisp map-cache
debug lisp control-plane
debug lisp data-plane
show lisp session
```

## Best Practices

### Design Guidelines
1. **Network Architecture**
   - Hierarchical design
   - Address planning
   - Mapping redundancy
   - Site distribution
   - Documentation

2. **Implementation Strategy**
   - Phased deployment
   - Testing methodology
   - Monitoring plan
   - Backup procedures
   - Change management

### Security Considerations
1. **Control Plane Security**
   ```
   ! Configure authentication
   Router(config)# router lisp
   Router(config-router-lisp)# site SITE1
   Router(config-router-lisp-site)# authentication-key hash-sha256 SECRET
   ```

2. **Data Plane Security**
   ```
   ! Configure RLOC probing
   Router(config)# router lisp
   Router(config-router-lisp)# rloc-probing
   Router(config-router-lisp)# map-cache-limit 1000
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
  - WAN design
  - IPv6 transition
  - Cloud connectivity
- Best practices:
  - Address planning
  - Mapping system design
  - Security implementation
  - Performance optimization
- Advanced concepts:
  - LISP-MN
  - LISP-DDT
  - LISP-SEC
  - LISP-TE 