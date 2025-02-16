# NAT/PAT (Network Address Translation/Port Address Translation)

## Overview
NAT/PAT is a method of remapping one IP address space into another by modifying network address information in the IP header of packets. It enables private networks to access public networks while conserving public IP addresses and providing an additional layer of security.

## Technical Details

### Types of NAT

1. **Static NAT**
   - One-to-one mapping
   - Fixed translations
   - Bidirectional access
   - Common for servers

2. **Dynamic NAT**
   - Many-to-many mapping
   - Address pool usage
   - First-come-first-served
   - Temporary translations

3. **PAT (Overload)**
   - Many-to-one mapping
   - Port number translation
   - Most common type
   - Also called NAT overload

4. **Twice NAT**
   - Source and destination
   - Simultaneous translation
   - Complex scenarios
   - Special applications

### NAT Terms
- Inside Local: Private address of internal host
- Inside Global: Public address representing internal host
- Outside Local: Address of external host as seen internally
- Outside Global: Actual address of external host

## Implementation

### Basic Configuration

1. **Static NAT**
   ```
   ! Define inside/outside interfaces
   Router(config)# interface GigabitEthernet0/0
   Router(config-if)# ip nat inside
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip nat outside
   
   ! Configure static mapping
   Router(config)# ip nat inside source static 192.168.1.10 203.0.113.10
   ```

2. **Dynamic NAT**
   ```
   ! Define NAT pool
   Router(config)# ip nat pool NAT-POOL 203.0.113.20 203.0.113.30 netmask 255.255.255.0
   
   ! Create access list for internal networks
   Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
   
   ! Configure dynamic NAT
   Router(config)# ip nat inside source list 1 pool NAT-POOL
   ```

3. **PAT Configuration**
   ```
   ! Configure PAT with single IP
   Router(config)# ip nat inside source list 1 interface GigabitEthernet0/1 overload
   ```

### Advanced Configuration

1. **Port Forwarding**
   ```
   ! Forward HTTP traffic
   Router(config)# ip nat inside source static tcp 192.168.1.10 80 203.0.113.10 80
   ```

2. **Twice NAT**
   ```
   ! Configure twice NAT
   Router(config)# ip nat source static 192.168.1.10 203.0.113.10
   Router(config)# ip nat destination static 203.0.113.20 192.168.1.20
   ```

## Design Considerations

### Network Planning
1. **Address Space**
   - Public IP requirements
   - Private addressing scheme
   - Translation capacity
   - Growth planning

2. **Performance**
   - Hardware capabilities
   - Translation table size
   - Timeout values
   - Session limits

3. **Scalability**
   - Multiple NAT devices
   - Load distribution
   - Redundancy
   - Failover

### High Availability
1. **Redundancy**
   - Dual NAT devices
   - State synchronization
   - Failover configuration
   - Load sharing

2. **Session Management**
   - Timeout optimization
   - Session tracking
   - State table sync
   - Recovery procedures

## Troubleshooting

### Common Issues
1. **Translation Problems**
   - Missing routes
   - ACL issues
   - Pool exhaustion
   - Asymmetric routing

2. **Performance Issues**
   - High CPU usage
   - Memory constraints
   - Table overflow
   - Latency issues

3. **Connectivity Problems**
   - Application failures
   - Protocol issues
   - Routing problems
   - State table issues

### Verification Commands
```
show ip nat translations
show ip nat statistics
debug ip nat
show ip nat translations verbose
clear ip nat translation
show running-config | include nat
```

## Best Practices

### Design Guidelines
1. **Address Planning**
   - Sufficient pool size
   - Appropriate timeouts
   - Conservative overload
   - Route optimization

2. **Security Measures**
   - Access control
   - Logging setup
   - State monitoring
   - Policy enforcement

3. **Performance Optimization**
   - Hardware selection
   - Table sizing
   - Timeout tuning
   - Load distribution

### Operational Procedures
1. **Maintenance**
   - Regular monitoring
   - Log analysis
   - Performance tuning
   - State table cleanup

2. **Documentation**
   - Translation policies
   - Port forwarding
   - Access controls
   - Troubleshooting procedures

## Security Considerations

### NAT Security
1. **Access Control**
   ```
   ! Restrict NAT access
   Router(config)# ip access-list extended NAT-ACL
   Router(config-ext-nacl)# permit ip 192.168.1.0 0.0.0.255 any
   Router(config)# ip nat inside source list NAT-ACL interface GigabitEthernet0/1 overload
   ```

2. **Logging**
   ```
   ! Enable NAT logging
   Router(config)# ip nat log translations syslog
   ```

## Interview Tips
- Understand NAT types and operation
- Know NAT terminology
- Explain translation process
- Understand security implications
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Enterprise deployment
  - Data center NAT
  - Service provider NAT
  - IPv4 to IPv6 transition
- Best practices:
  - Address planning
  - Performance tuning
  - Security implementation
  - High availability
- Advanced concepts:
  - NAT64
  - Carrier Grade NAT
  - NAT traversal
  - Application Layer Gateway 