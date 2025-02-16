# NetFlow

## Overview
NetFlow is a network protocol developed by Cisco for collecting IP traffic information and monitoring network flow. It provides network administrators with detailed insights into network traffic patterns, enabling traffic accounting, usage-based network billing, network planning, security, Denial of Service monitoring, and network monitoring.

## Technical Details

### Components

1. **Flow Exporter**
   - Network devices
   - Traffic monitoring
   - Flow creation
   - Data export
   - Cache management

2. **Flow Collector**
   - Data reception
   - Data processing
   - Data storage
   - Data aggregation
   - Report generation

3. **Flow Record**
   - Source IP
   - Destination IP
   - Source port
   - Destination port
   - Protocol
   - Interface
   - Bytes/Packets
   - Timestamps

### Protocol Versions

1. **NetFlow v5**
   - Fixed format
   - Basic fields
   - Wide support
   - Limited features

2. **NetFlow v9**
   - Template-based
   - Flexible format
   - IPv6 support
   - MPLS support
   - VPN support

3. **IPFIX (v10)**
   - IETF standard
   - Enhanced templates
   - Variable fields
   - Enterprise elements
   - Bidirectional flows

### Flow Types

1. **Traditional NetFlow**
   - Per-packet processing
   - Flow cache
   - Complete data
   - High resource usage

2. **Sampled NetFlow**
   - Packet sampling
   - Reduced overhead
   - Statistical analysis
   - Resource efficient

3. **Flexible NetFlow**
   - Custom flow records
   - Multiple caches
   - Advanced features
   - Enhanced visibility

## Implementation

### Basic Configuration

1. **Cisco Router Setup**
   ```
   ! Enable NetFlow on interface
   interface GigabitEthernet0/0
    ip flow ingress
    ip flow egress
   
   ! Configure global NetFlow
   ip flow-export version 9
   ip flow-export destination 192.168.1.100 2055
   ip flow-export source Loopback0
   ip flow-cache timeout active 1
   ip flow-cache timeout inactive 15
   ```

2. **Collector Setup**
   ```bash
   # Install nfdump tools
   apt-get install nfdump
   
   # Start NetFlow collector
   nfcapd -w -D -l /var/log/netflow -p 2055
   
   # Configure storage
   mkdir -p /var/log/netflow
   chown nobody:nobody /var/log/netflow
   ```

### Advanced Configuration

1. **Flexible NetFlow**
   ```
   ! Define flow record
   flow record CUSTOM_RECORD
    match ipv4 source address
    match ipv4 destination address
    match transport source-port
    match transport destination-port
    match protocol
    collect counter bytes
    collect counter packets
    collect timestamp sys-uptime first
    collect timestamp sys-uptime last
   
   ! Define flow monitor
   flow monitor CUSTOM_MONITOR
    record CUSTOM_RECORD
    cache timeout active 60
    cache timeout inactive 15
   
   ! Apply to interface
   interface GigabitEthernet0/0
    ip flow monitor CUSTOM_MONITOR input
    ip flow monitor CUSTOM_MONITOR output
   ```

2. **Sampled NetFlow**
   ```
   ! Configure sampling
   flow-sampler-map SAMPLE_MAP
    mode random one-out-of 100
   
   ! Apply to interface
   interface GigabitEthernet0/0
    flow-sampler SAMPLE_MAP
   ```

## Design Considerations

### Architecture Planning
1. **Deployment Strategy**
   - Collection points
   - Sampling rates
   - Export intervals
   - Storage requirements

2. **Scalability**
   - Collector capacity
   - Network bandwidth
   - Processing power
   - Storage growth

3. **Integration**
   - Analysis tools
   - Monitoring systems
   - Security platforms
   - Reporting tools

### Performance Planning
1. **Resource Management**
   - CPU utilization
   - Memory usage
   - Network overhead
   - Storage capacity

2. **Data Management**
   - Retention periods
   - Aggregation levels
   - Query optimization
   - Backup strategy

## Troubleshooting

### Common Issues
1. **Export Problems**
   - Connectivity issues
   - Template mismatch
   - Version incompatibility
   - Buffer overflow

2. **Collection Issues**
   - Missing flows
   - Incomplete data
   - Delayed exports
   - Processing delays

3. **Performance Problems**
   - High CPU usage
   - Memory exhaustion
   - Network congestion
   - Storage limitations

### Verification Commands
```
! Check NetFlow statistics
show ip flow export
show ip cache flow
show flow monitor

! Verify interface configuration
show ip flow interface

! Debug NetFlow
debug ip flow export
debug ip flow cache

! Collector verification
nfdump -R /var/log/netflow -s srcip
nfdump -R /var/log/netflow -s dstip
```

## Best Practices

### Configuration Guidelines
1. **Flow Export**
   - Regular intervals
   - Multiple collectors
   - Source interface
   - Version selection

2. **Sampling Configuration**
   - Appropriate rates
   - Consistent sampling
   - Resource monitoring
   - Performance tuning

3. **Data Management**
   - Regular archival
   - Data compression
   - Query optimization
   - Storage planning

### Operational Procedures
1. **Maintenance**
   - Regular backups
   - Performance monitoring
   - Cache management
   - Version updates

2. **Documentation**
   - Configuration details
   - Collection points
   - Retention policies
   - Analysis procedures

## Security Considerations

### NetFlow Security
1. **Export Security**
   ```
   ! Configure export source
   ip flow-export source Loopback0
   
   ! Enable SCTP transport
   ip flow-export destination 192.168.1.100 2055 sctp
   
   ! Configure export filtering
   ip flow-export filter destination-prefix 192.168.1.0/24
   ```

2. **Access Control**
   ```
   ! Restrict SNMP access for NetFlow
   access-list 10 permit 192.168.1.100
   snmp-server community private RO 10
   
   ! Secure collector access
   iptables -A INPUT -p udp --dport 2055 -s 192.168.1.0/24 -j ACCEPT
   iptables -A INPUT -p udp --dport 2055 -j DROP
   ```

## Interview Tips
- Understand NetFlow architecture
- Know protocol versions
- Explain flow records
- Understand sampling concepts
- Be familiar with:
  - Flow components
  - Configuration options
  - Collection methods
  - Analysis techniques
- Real-world scenarios:
  - Traffic analysis
  - Capacity planning
  - Security monitoring
  - Billing systems
- Best practices:
  - Deployment strategy
  - Resource management
  - Data collection
  - Performance optimization
- Advanced concepts:
  - Flexible NetFlow
  - IPFIX
  - Flow analysis
  - Custom templates 