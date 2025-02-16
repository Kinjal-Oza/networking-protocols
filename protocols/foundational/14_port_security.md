# Port Security

## Overview
Port Security is a Layer 2 security feature that enables control over what MAC addresses can access a switch port, helping prevent unauthorized access and MAC address flooding attacks.

## Technical Details

### Security Features
- MAC address learning
- MAC address limiting
- Sticky MAC addresses
- Violation handling
- VLAN-aware security

### Security Parameters

1. **MAC Address Limits**
   - Maximum addresses per port
   - Static vs dynamic learning
   - Sticky learning option
   - VLAN-specific limits

2. **Violation Modes**
   - Protect: Drops packets
   - Restrict: Drops and logs
   - Shutdown: Disables port
   - Shutdown VLAN: Disables VLAN

3. **MAC Address Types**
   - Static: Manually configured
   - Dynamic: Learned from traffic
   - Sticky: Learned and saved
   - Reserved: Special purposes

## Implementation

### Basic Configuration

1. **Enable Port Security**
   ```
   ! Enable port security
   Switch(config-if)# switchport port-security
   
   ! Set maximum addresses
   Switch(config-if)# switchport port-security maximum 2
   
   ! Configure violation mode
   Switch(config-if)# switchport port-security violation restrict
   ```

2. **Static MAC Configuration**
   ```
   ! Configure static MAC address
   Switch(config-if)# switchport port-security mac-address 0000.1111.2222
   
   ! Enable sticky learning
   Switch(config-if)# switchport port-security mac-address sticky
   ```

### Advanced Configuration

1. **VLAN-Aware Security**
   ```
   ! Configure per-VLAN limits
   Switch(config-if)# switchport port-security maximum 1 vlan 10
   Switch(config-if)# switchport port-security maximum 2 vlan 20
   ```

2. **Aging Configuration**
   ```
   ! Configure aging time
   Switch(config-if)# switchport port-security aging time 60
   
   ! Configure aging type
   Switch(config-if)# switchport port-security aging type inactivity
   ```

## Design Considerations

### Security Planning
1. **Access Requirements**
   - Number of devices
   - Device mobility
   - VLAN requirements

2. **Violation Handling**
   - Business impact
   - Logging requirements
   - Recovery procedures

3. **Maintenance Planning**
   - MAC address updates
   - Configuration backup
   - Recovery procedures

### Implementation Strategy
1. **Deployment Phases**
   - Initial monitoring
   - Gradual enforcement
   - Full implementation

2. **Documentation**
   - MAC address inventory
   - Port assignments
   - Security policies

## Troubleshooting

### Common Issues
1. **Port Violations**
   - Unauthorized MAC addresses
   - Exceeded limits
   - VLAN violations

2. **Configuration Problems**
   - Incorrect MAC addresses
   - Wrong violation mode
   - Aging issues

3. **Operational Issues**
   - Port shutdown
   - MAC address changes
   - Recovery procedures

### Verification Commands
```
show port-security
show port-security interface
show port-security address
show port-security interface fastethernet 0/1 address
show running-config interface
```

## Best Practices

### Design Guidelines
1. **Port Configuration**
   - Appropriate MAC limits
   - Suitable violation mode
   - Aging configuration

2. **Security Policy**
   - Clear violation procedures
   - Documentation requirements
   - Change management

3. **Monitoring Strategy**
   - Regular audits
   - Violation tracking
   - Performance impact

### Operational Procedures
1. **MAC Management**
   - Regular review
   - Update process
   - Documentation

2. **Violation Handling**
   - Response procedures
   - Recovery steps
   - Documentation

3. **Maintenance**
   - Configuration backup
   - Regular verification
   - Update procedures

## Security Considerations

### Attack Prevention
1. **MAC Spoofing**
   - Sticky MAC addresses
   - Limited learning
   - Violation logging

2. **CAM Table Overflow**
   - Maximum limits
   - Port shutdown
   - VLAN protection

### Monitoring and Logging
1. **SNMP Configuration**
   ```
   Switch(config)# snmp-server enable traps port-security
   Switch(config)# snmp-server host 192.168.1.100 version 2c public port-security
   ```

2. **Syslog Settings**
   ```
   Switch(config)# logging host 192.168.1.100
   Switch(config)# logging trap notifications
   ```

## Recovery Procedures

### Port Recovery
1. **Manual Recovery**
   ```
   ! Clear port security violation
   Switch# clear port-security interface fastethernet 0/1
   
   ! Re-enable interface
   Switch(config)# interface fastethernet 0/1
   Switch(config-if)# shutdown
   Switch(config-if)# no shutdown
   ```

2. **Automatic Recovery**
   ```
   ! Configure error-disable recovery
   Switch(config)# errdisable recovery cause psecure-violation
   Switch(config)# errdisable recovery interval 300
   ```

## Interview Tips
- Understand port security concepts
- Know configuration options
- Explain violation modes
- Understand recovery procedures
- Be familiar with:
  - MAC address types
  - Violation handling
  - Troubleshooting steps
  - Best practices
- Real-world scenarios:
  - Access layer security
  - BYOD environments
  - Compliance requirements
- Security considerations:
  - Attack prevention
  - Logging requirements
  - Recovery procedures
- Implementation strategy:
  - Phased deployment
  - Policy development
  - Operational procedures 