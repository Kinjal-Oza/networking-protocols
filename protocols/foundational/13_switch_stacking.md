# Switch Stacking

## Overview
Switch stacking is a technology that allows multiple physical switches to operate as a single logical unit, providing unified management, increased redundancy, and simplified network operations.

## Technical Details

### Stack Characteristics
- Single management interface
- Unified control plane
- Shared configuration
- Automatic topology discovery
- Stack member roles (Master/Standby)

### Stack Components

1. **Stack Members**
   - Master switch
   - Standby switch
   - Member switches
   - Maximum members (platform-specific)

2. **Stack Connections**
   - Dedicated stacking ports
   - Stack cables/modules
   - Ring or daisy-chain topology
   - Bandwidth (platform-specific)

3. **Stack Protocol**
   - Discovery mechanism
   - Role negotiation
   - Configuration synchronization
   - State maintenance

## Implementation

### Basic Configuration

1. **Stack Preparation**
   ```
   ! Set switch priority
   Switch(config)# switch 1 priority 15
   
   ! Configure stack ports
   Switch(config)# stack-port 1/1 enable
   
   ! Set switch number
   Switch(config)# switch 2 renumber 3
   ```

2. **Stack Management**
   ```
   ! Configure stack name
   Switch(config)# stack-name Production-Stack
   
   ! Set stack MAC address
   Switch(config)# stack-mac persistent timer 0
   ```

### Advanced Configuration

1. **Stack Master Election**
   ```
   ! Configure priority
   Switch(config)# switch 1 priority 15
   Switch(config)# switch 2 priority 14
   ```

2. **Stack Interface Configuration**
   ```
   ! Configure cross-stack EtherChannel
   Switch(config)# interface range GigabitEthernet1/0/1, GigabitEthernet2/0/1
   Switch(config-if-range)# channel-group 1 mode active
   ```

## Design Considerations

### Capacity Planning
1. **Stack Size**
   - Number of switches
   - Port density requirements
   - Growth projections

2. **Bandwidth Requirements**
   - Stack link capacity
   - Traffic patterns
   - Oversubscription ratios

3. **Power Planning**
   - Power redundancy
   - Power budget
   - Cooling requirements

### High Availability
1. **Stack Topology**
   - Full ring recommended
   - Redundant connections
   - Cable length limitations

2. **Master Election**
   - Priority configuration
   - Standby selection
   - Failover scenarios

3. **Stack Resilience**
   - Member failure handling
   - Link failure recovery
   - Configuration backup

## Troubleshooting

### Common Issues
1. **Stack Formation**
   - Version mismatches
   - Cabling problems
   - Role conflicts

2. **Stack Operations**
   - Master election issues
   - Configuration sync
   - Member joining/leaving

3. **Performance Problems**
   - Stack bandwidth
   - Traffic distribution
   - Resource utilization

### Verification Commands
```
show switch
show switch stack-ports
show switch stack-ring activity
show platform stack manager all
show stack-power
debug switch stack
```

## Best Practices

### Design Guidelines
1. **Stack Architecture**
   - Use ring topology
   - Plan for redundancy
   - Consider growth

2. **Member Selection**
   - Compatible models
   - Consistent software
   - Matching capabilities

3. **Configuration Management**
   - Regular backups
   - Version control
   - Change documentation

### Operational Procedures
1. **Stack Maintenance**
   - Scheduled updates
   - Member replacement
   - Configuration changes

2. **Monitoring**
   - Stack health
   - Performance metrics
   - Error conditions

3. **Documentation**
   - Physical topology
   - Member inventory
   - Configuration details

## Security Considerations

### Access Control
1. **Management Security**
   ```
   ! Configure stack management access
   Switch(config)# username admin privilege 15 secret stackadmin
   Switch(config)# line vty 0 15
   Switch(config-line)# transport input ssh
   ```

2. **Stack Protocol Security**
   ```
   ! Enable stack protocol security
   Switch(config)# stack-port security
   ```

### Monitoring and Logging
1. **SNMP Configuration**
   ```
   Switch(config)# snmp-server enable traps stack
   Switch(config)# snmp-server host 192.168.1.100 version 2c public stack
   ```

2. **Syslog Settings**
   ```
   Switch(config)# logging host 192.168.1.100
   Switch(config)# logging trap notifications
   ```

## Interview Tips
- Understand stack operation principles
- Know member roles and election process
- Explain high availability features
- Understand failure scenarios
- Be familiar with:
  - Configuration commands
  - Troubleshooting methods
  - Design considerations
  - Platform limitations
- Real-world scenarios:
  - Campus deployment
  - Access layer design
  - Data center implementation
- Best practices:
  - Stack planning
  - Redundancy design
  - Maintenance procedures
- Performance considerations:
  - Stack bandwidth
  - Traffic distribution
  - Resource allocation 