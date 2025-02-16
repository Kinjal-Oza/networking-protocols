# GLBP (Gateway Load Balancing Protocol)

## Overview
GLBP is a Cisco proprietary protocol that provides both load balancing and redundancy for IPv4 networks. Unlike HSRP and VRRP, which only allow one active gateway, GLBP allows multiple routers to simultaneously forward traffic while acting as a single virtual gateway.

## Technical Details

### Protocol Characteristics
- Layer: 3 (Network)
- UDP Port: 3222
- Virtual MAC: 0007.b400.XXYY (XX = group, YY = forwarder)
- Multicast Address: 224.0.0.102
- Active Virtual Gateway (AVG)
- Active Virtual Forwarder (AVF)

### GLBP Components

1. **Router Roles**
   - Active Virtual Gateway (AVG)
   - Standby Virtual Gateway
   - Active Virtual Forwarder (AVF)
   - Alternate Virtual Forwarder (ALF)

2. **States**
   - Initial
   - Listen
   - Speak
   - Standby
   - Active

3. **Load Balancing Methods**
   - Round-robin
   - Weighted
   - Host-dependent
   - Route-based

### GLBP Features

1. **Load Balancing**
   - Multiple active forwarders
   - Automatic load distribution
   - Configurable weights
   - Dynamic adjustment

2. **High Availability**
   - Gateway redundancy
   - Forwarder redundancy
   - Automatic failover
   - Preemption support

## Implementation

### Basic Configuration

1. **Enable GLBP**
   ```
   ! Configure interface
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip address 192.168.1.1 255.255.255.0
   
   ! Configure GLBP
   Router(config-if)# glbp 1 ip 192.168.1.254
   Router(config-if)# glbp 1 priority 150
   Router(config-if)# glbp 1 preempt
   ```

2. **Load Balancing Configuration**
   ```
   ! Configure load balancing method
   Router(config-if)# glbp 1 load-balancing round-robin
   
   ! Configure weighted load balancing
   Router(config-if)# glbp 1 load-balancing weighted
   Router(config-if)# glbp 1 weighting 100 lower 30 upper 90
   ```

### Advanced Configuration

1. **Authentication**
   ```
   ! Configure MD5 authentication
   Router(config-if)# glbp 1 authentication md5 key-string GLBP_KEY
   
   ! Configure key chain
   Router(config)# key chain GLBP-KEYS
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECURE_KEY
   Router(config-if)# glbp 1 authentication md5 key-chain GLBP-KEYS
   ```

2. **Interface Tracking**
   ```
   ! Configure interface tracking
   Router(config-if)# glbp 1 weighting track 1 decrement 30
   
   ! Configure tracked object
   Router(config)# track 1 interface GigabitEthernet0/2 line-protocol
   ```

## Design Considerations

### Network Planning
1. **Topology Design**
   - Router placement
   - Group assignment
   - Virtual IP addressing
   - Load distribution

2. **Performance Impact**
   - Hello packet overhead
   - CPU utilization
   - Convergence time
   - Bandwidth usage

3. **Scalability**
   - Number of groups
   - Number of forwarders
   - Memory requirements
   - Network size

### High Availability
1. **Redundancy**
   - Multiple forwarders
   - Backup AVG
   - Interface tracking
   - Fast failover

2. **Load Balancing**
   - Method selection
   - Weight configuration
   - Traffic distribution
   - Path optimization

## Troubleshooting

### Common Issues
1. **State Problems**
   - Multiple AVGs
   - Forwarder issues
   - State transitions
   - Priority conflicts

2. **Communication Issues**
   - Authentication failure
   - Timer mismatches
   - Network connectivity
   - Hello packet loss

3. **Load Balancing Problems**
   - Uneven distribution
   - Weight issues
   - Method conflicts
   - Tracking failures

### Verification Commands
```bash
# Show GLBP status
show glbp
show glbp brief
show glbp all

# Debug GLBP operations
debug glbp events
debug glbp errors
debug glbp packets

# Show tracking information
show track
show glbp internal
```

## Best Practices

### Design Guidelines
1. **Group Configuration**
   ```
   ! Configure optimal settings
   Router(config-if)# glbp 1 timers msec 250 msec 750
   Router(config-if)# glbp 1 timers redirect 600 300
   ```

2. **Load Balancing Setup**
   ```
   ! Configure weighted load balancing
   Router(config-if)# glbp 1 load-balancing weighted
   Router(config-if)# glbp 1 weighting 100 lower 30 upper 90
   Router(config-if)# glbp 1 weighting track 1 decrement 30
   ```

### Operational Procedures
1. **Monitoring Setup**
   - State monitoring
   - Load distribution
   - Event logging
   - Performance metrics

2. **Documentation**
   - Group assignments
   - Virtual IPs
   - Load balancing method
   - Weight configuration

## Security Considerations

### GLBP Security
1. **Authentication**
   ```
   ! Configure MD5 authentication
   Router(config-if)# glbp 1 authentication md5 key-string SECURE_KEY
   
   ! Configure key chain
   Router(config)# key chain GLBP-KEYS
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string COMPLEX_KEY
   ```

2. **Access Control**
   ```
   ! Configure ACL for GLBP
   Router(config)# access-list 101 permit udp any host 224.0.0.102 eq 3222
   Router(config-if)# ip access-group 101 in
   ```

## Interview Tips
- Understand GLBP operation
- Know router roles
- Explain load balancing methods
- Understand failover process
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Gateway redundancy
  - Load balancing
  - Interface tracking
  - Fast convergence
- Best practices:
  - Authentication setup
  - Timer configuration
  - Weight optimization
  - Group design
- Advanced concepts:
  - Multiple forwarders
  - Object tracking
  - Load distribution
  - Weighted balancing
- Comparison with HSRP/VRRP:
  - Load balancing capability
  - Active forwarders
  - Configuration complexity
  - Feature differences 