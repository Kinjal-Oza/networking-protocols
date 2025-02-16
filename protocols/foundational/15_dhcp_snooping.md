# DHCP Snooping

## Overview
DHCP Snooping is a Layer 2 security feature that acts like a firewall between untrusted hosts and DHCP servers, preventing various DHCP-based attacks and unauthorized DHCP servers.

## Technical Details

### Protocol Characteristics
- Layer: 2 (Data Link)
- DHCP message inspection
- Trust boundary enforcement
- Binding database maintenance
- Option 82 support

### Security Features

1. **Trust Levels**
   - Trusted ports (DHCP servers)
   - Untrusted ports (client access)
   - Rate limiting
   - Message validation

2. **Binding Database**
   - MAC address
   - IP address
   - VLAN
   - Interface
   - Lease time

3. **Message Validation**
   - Source MAC verification
   - DHCP message types
   - Option 82 handling
   - Rate limits

## Implementation

### Basic Configuration

1. **Enable DHCP Snooping**
   ```
   ! Enable globally
   Switch(config)# ip dhcp snooping
   
   ! Enable on specific VLANs
   Switch(config)# ip dhcp snooping vlan 10,20
   ```

2. **Configure Trusted Ports**
   ```
   ! Configure trusted interface
   Switch(config)# interface gigabitethernet 0/1
   Switch(config-if)# ip dhcp snooping trust
   
   ! Configure rate limit
   Switch(config-if)# ip dhcp snooping limit rate 100
   ```

### Advanced Configuration

1. **Option 82**
   ```
   ! Enable option 82
   Switch(config)# ip dhcp snooping information option
   
   ! Configure circuit-id format
   Switch(config)# ip dhcp snooping information option format remote-id string switch123
   ```

2. **Database Configuration**
   ```
   ! Configure database agent
   Switch(config)# ip dhcp snooping database flash:/dhcp-snooping-db
   
   ! Configure write-delay
   Switch(config)# ip dhcp snooping database write-delay 300
   ```

## Design Considerations

### Network Planning
1. **Trust Boundary**
   - DHCP server locations
   - Uplink connections
   - Distribution layer

2. **VLAN Design**
   - DHCP scope planning
   - VLAN segregation
   - Client isolation

3. **Capacity Planning**
   - Number of clients
   - Rate limiting
   - Database size

### High Availability
1. **Database Redundancy**
   - Backup location
   - Synchronization
   - Recovery procedures

2. **Server Redundancy**
   - Multiple DHCP servers
   - Trust configuration
   - Failover planning

## Security Considerations

### Attack Prevention
1. **Rogue DHCP Server**
   - Trust boundary
   - Port security
   - VLAN isolation

2. **DHCP Starvation**
   - Rate limiting
   - Port security
   - MAC verification

3. **Man-in-the-Middle**
   - Option 82
   - Source verification
   - Binding database

### Monitoring Configuration
1. **SNMP Settings**
   ```
   Switch(config)# snmp-server enable traps dhcp-snooping
   Switch(config)# snmp-server host 192.168.1.100 version 2c public dhcp-snooping
   ```

2. **Syslog Configuration**
   ```
   Switch(config)# logging host 192.168.1.100
   Switch(config)# logging trap notifications
   ```

## Troubleshooting

### Common Issues
1. **Client IP Assignment**
   - DHCP message drops
   - Rate limit exceeded
   - Trust issues

2. **Database Problems**
   - Write failures
   - Corruption
   - Synchronization

3. **Option 82 Issues**
   - Format problems
   - Relay issues
   - Server compatibility

### Verification Commands
```
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping database
show ip dhcp snooping statistics
debug ip dhcp snooping
```

## Best Practices

### Design Guidelines
1. **Trust Configuration**
   - Minimal trusted ports
   - Clear documentation
   - Regular audit

2. **Rate Limiting**
   - Appropriate thresholds
   - Monitor violations
   - Adjust as needed

3. **Database Management**
   - Regular backup
   - Monitoring
   - Cleanup procedures

### Operational Procedures
1. **Change Management**
   - Trust changes
   - Rate limit adjustments
   - Database maintenance

2. **Monitoring**
   - Violation tracking
   - Database status
   - Performance impact

3. **Documentation**
   - Trust boundary
   - Rate limits
   - Configuration details

## Integration

### Related Features
1. **Dynamic ARP Inspection**
   ```
   Switch(config)# ip arp inspection vlan 10
   Switch(config-if)# ip arp inspection trust
   ```

2. **IP Source Guard**
   ```
   Switch(config-if)# ip verify source
   Switch(config-if)# ip verify source port-security
   ```

### DHCP Server Configuration
1. **Option 82 Support**
   ```
   ! Configure DHCP server for option 82
   Router(config)# ip dhcp pool POOL1
   Router(dhcp-config)# relay information policy keep
   ```

## Interview Tips
- Understand DHCP Snooping purpose
- Know trust boundary concept
- Explain binding database
- Understand Option 82
- Be familiar with:
  - Configuration commands
  - Security features
  - Troubleshooting methods
  - Integration points
- Real-world scenarios:
  - Enterprise deployment
  - DHCP security
  - Attack prevention
- Best practices:
  - Trust configuration
  - Rate limiting
  - Database management
- Integration knowledge:
  - DAI
  - IP Source Guard
  - DHCP server configuration 