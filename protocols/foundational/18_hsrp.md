# HSRP (Hot Standby Router Protocol)

## Overview
HSRP is a Cisco proprietary redundancy protocol that enables high network availability by providing first-hop redundancy for IP hosts on Ethernet networks configured with a default gateway IP address. It allows multiple routers to present themselves as a single virtual router.

## Technical Details

### Protocol Characteristics
- Layer: 3 (Network)
- UDP Port: 1985
- Virtual MAC: 0000.0C07.ACxx (xx = group number)
- Multicast Address: 224.0.0.2
- Version 1 and Version 2 support
- Active/Standby architecture

### HSRP Components

1. **Router Roles**
   - Active Router
   - Standby Router
   - Virtual Router
   - Other routers (Listen state)

2. **States**
   - Initial
   - Learn
   - Listen
   - Speak
   - Standby
   - Active

3. **Timers**
   - Hello: 3 seconds
   - Hold: 10 seconds
   - Preemption delay
   - Authentication

### HSRP Versions

1. **Version 1**
   - Group range: 0-255
   - No IPv6 support
   - Basic functionality
   - Limited features

2. **Version 2**
   - Group range: 0-4095
   - IPv6 support
   - Enhanced timers
   - Extended features

## Implementation

### Basic Configuration

1. **Enable HSRP**
   ```
   ! Configure interface
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip address 192.168.1.1 255.255.255.0
   
   ! Configure HSRP
   Router(config-if)# standby 1 ip 192.168.1.254
   Router(config-if)# standby 1 priority 110
   Router(config-if)# standby 1 preempt
   ```

2. **Version Selection**
   ```
   ! Configure HSRP version
   Router(config-if)# standby version 2
   
   ! Configure group
   Router(config-if)# standby 1 ipv6 FE80::5:1
   ```

### Advanced Configuration

1. **Authentication**
   ```
   ! Configure MD5 authentication
   Router(config-if)# standby 1 authentication md5 key-string HSRP_KEY
   
   ! Configure key chain
   Router(config)# key chain HSRP-KEYS
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECURE_KEY
   Router(config-if)# standby 1 authentication md5 key-chain HSRP-KEYS
   ```

2. **Interface Tracking**
   ```
   ! Configure interface tracking
   Router(config-if)# standby 1 track GigabitEthernet0/2 30
   
   ! Configure object tracking
   Router(config)# track 1 interface GigabitEthernet0/2 line-protocol
   Router(config-if)# standby 1 track 1 decrement 30
   ```

## Design Considerations

### Network Planning
1. **Topology Design**
   - Router placement
   - Group assignment
   - Virtual IP addressing
   - Interface selection

2. **Performance Impact**
   - Hello packet overhead
   - CPU utilization
   - Convergence time
   - Bandwidth usage

3. **Scalability**
   - Number of groups
   - Interface capacity
   - Memory requirements
   - Network size

### High Availability
1. **Redundancy**
   - Multiple groups
   - Load sharing
   - Interface tracking
   - Fast failover

2. **Load Balancing**
   - Multiple HSRP groups
   - Priority configuration
   - Traffic distribution
   - Path optimization

## Troubleshooting

### Common Issues
1. **State Problems**
   - Multiple active routers
   - No standby router
   - State transitions
   - Priority conflicts

2. **Communication Issues**
   - Authentication failure
   - Timer mismatches
   - Version incompatibility
   - Network connectivity

3. **Tracking Problems**
   - Interface state
   - Decrement values
   - Object tracking
   - Preemption issues

### Verification Commands
```bash
# Show HSRP status
show standby
show standby brief
show standby all

# Debug HSRP operations
debug standby
debug standby events
debug standby errors

# Show tracking information
show track
show standby internal
```

## Best Practices

### Design Guidelines
1. **Group Configuration**
   ```
   ! Configure multiple groups
   Router(config-if)# standby 1 ip 192.168.1.254
   Router(config-if)# standby 1 priority 110
   Router(config-if)# standby 2 ip 192.168.1.253
   Router(config-if)# standby 2 priority 90
   ```

2. **Timer Optimization**
   ```
   ! Configure optimal timers
   Router(config-if)# standby 1 timers msec 200 msec 750
   Router(config-if)# standby 1 preempt delay minimum 60
   ```

### Operational Procedures
1. **Monitoring Setup**
   - State monitoring
   - Interface tracking
   - Event logging
   - Performance metrics

2. **Documentation**
   - Group assignments
   - Virtual IPs
   - Authentication keys
   - Tracking configuration

## Security Considerations

### HSRP Security
1. **Authentication**
   ```
   ! Configure authentication
   Router(config-if)# standby 1 authentication md5 key-string SECURE_KEY
   
   ! Configure key chain
   Router(config)# key chain HSRP-KEYS
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string COMPLEX_KEY
   ```

2. **Access Control**
   ```
   ! Configure ACL for HSRP
   Router(config)# access-list 101 permit udp host 192.168.1.1 host 224.0.0.2 eq 1985
   Router(config)# access-list 101 permit udp host 192.168.1.2 host 224.0.0.2 eq 1985
   ```

## Interview Tips
- Understand HSRP operation
- Know router states
- Explain failover process
- Understand version differences
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Gateway redundancy
  - Load sharing
  - Interface tracking
  - Fast convergence
- Best practices:
  - Authentication setup
  - Timer configuration
  - Tracking implementation
  - Group design
- Advanced concepts:
  - Multiple groups
  - Object tracking
  - IPv6 support
  - Load balancing 