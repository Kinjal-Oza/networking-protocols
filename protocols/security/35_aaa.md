# AAA (Authentication, Authorization, and Accounting)

## Overview
AAA is a framework for intelligently controlling access to computer resources, enforcing policies, auditing usage, and providing information necessary for billing of services. It provides three essential security services: Authentication (who are you?), Authorization (what can you do?), and Accounting (what did you do?).

## Technical Details

### Components

1. **Authentication**
   - Username/password
   - Certificates
   - Biometrics
   - Multi-factor authentication
   - Single Sign-On (SSO)

2. **Authorization**
   - Role-based access control (RBAC)
   - Attribute-based access control (ABAC)
   - Access control lists (ACL)
   - Policy enforcement
   - Resource permissions

3. **Accounting**
   - Session tracking
   - Resource usage
   - Audit trails
   - Billing information
   - Compliance logging

### Protocols

1. **RADIUS**
   - UDP-based
   - Client-server model
   - Encryption methods
   - Attribute-value pairs
   - Port numbers (1812, 1813)

2. **TACACS+**
   - TCP-based
   - Cisco proprietary
   - Encrypted payload
   - Separate authentication
   - Port number (49)

3. **Diameter**
   - TCP/SCTP-based
   - Enhanced RADIUS
   - Peer-to-peer
   - Application support
   - Failover handling

## Implementation

### Basic Configuration

1. **RADIUS Server Setup**
   ```bash
   # /etc/freeradius/3.0/clients.conf
   client localhost {
       ipaddr = 127.0.0.1
       secret = testing123
       require_message_authenticator = yes
   }
   
   # /etc/freeradius/3.0/users
   test_user Cleartext-Password := "password"
       Service-Type = Login-User,
       Cisco-AVPair = "shell:priv-lvl=15"
   ```

2. **TACACS+ Server Setup**
   ```bash
   # /etc/tacacs+/tac_plus.conf
   key = "tacacskey"
   
   user = admin {
       default service = permit
       login = cleartext "password"
       service = exec {
           priv-lvl = 15
       }
   }
   ```

### Advanced Configuration

1. **Cisco Device AAA**
   ```
   aaa new-model
   
   ! RADIUS Configuration
   aaa group server radius RAD_GROUP
    server 192.168.1.100 auth-port 1812 acct-port 1813
   
   ! TACACS+ Configuration
   aaa group server tacacs+ TAC_GROUP
    server 192.168.1.200
   
   aaa authentication login default group TAC_GROUP local
   aaa authorization exec default group TAC_GROUP local
   aaa accounting exec default start-stop group TAC_GROUP
   ```

2. **Multi-Factor Authentication**
   ```
   ! Enable MFA
   aaa authentication login default group radius local
   
   radius-server host 192.168.1.100 key secretkey
   radius-server attribute 8 include-in-access-req
   
   login authentication default
   ```

## Design Considerations

### Architecture Planning
1. **Server Deployment**
   - Centralized vs distributed
   - High availability
   - Geographic distribution
   - Load balancing

2. **Protocol Selection**
   - RADIUS vs TACACS+
   - Authentication methods
   - Encryption requirements
   - Performance needs

3. **Integration**
   - Directory services
   - Identity providers
   - Network devices
   - Applications

### Security Planning
1. **Access Control**
   - Role definition
   - Policy management
   - Resource mapping
   - Privilege levels

2. **Audit Requirements**
   - Log retention
   - Report generation
   - Compliance needs
   - Storage planning

## Troubleshooting

### Common Issues
1. **Authentication Problems**
   - Failed logins
   - Timeout issues
   - Protocol mismatches
   - Key problems

2. **Authorization Issues**
   - Incorrect privileges
   - Missing attributes
   - Policy conflicts
   - Group membership

3. **Accounting Problems**
   - Missing logs
   - Incomplete records
   - Storage issues
   - Performance impact

### Verification Commands
```bash
# Test RADIUS authentication
radtest user password localhost 0 testing123

# Debug TACACS+
tac_plus -d 16384

# Cisco IOS debug commands
debug aaa authentication
debug aaa authorization
debug radius
debug tacacs
```

## Best Practices

### Security Guidelines
1. **Server Security**
   - Secure protocols
   - Strong encryption
   - Key management
   - Access control

2. **Client Configuration**
   - Timeout settings
   - Retry limits
   - Fallback methods
   - Dead criteria

3. **Policy Management**
   - Clear definitions
   - Regular reviews
   - Change control
   - Documentation

### Operational Procedures
1. **Maintenance**
   - Regular updates
   - Log rotation
   - Performance monitoring
   - Backup procedures

2. **Documentation**
   - Configuration details
   - Policy documents
   - Troubleshooting guides
   - Recovery procedures

## Security Considerations

### AAA Security
1. **Server Hardening**
   ```bash
   # RADIUS server security
   chmod 600 /etc/freeradius/3.0/clients.conf
   chown freerad:freerad /etc/freeradius/3.0/*
   
   # TACACS+ server security
   chmod 600 /etc/tacacs+/tac_plus.conf
   ```

2. **Network Security**
   ```
   ! Enable encryption
   radius-server key 7 <encrypted-key>
   tacacs-server key 7 <encrypted-key>
   
   ! Source interface binding
   ip radius source-interface Loopback0
   ip tacacs source-interface Loopback0
   ```

## Interview Tips
- Understand AAA framework
- Know protocol differences
- Explain security features
- Understand deployment models
- Be familiar with:
  - Authentication methods
  - Authorization models
  - Accounting requirements
  - Protocol selection
- Real-world scenarios:
  - Network access
  - Device administration
  - Service authorization
  - Compliance requirements
- Best practices:
  - Server deployment
  - Policy management
  - Security implementation
  - Performance optimization
- Advanced concepts:
  - Change of Authorization (CoA)
  - Dynamic Authorization
  - Proxy operations
  - Attribute manipulation 