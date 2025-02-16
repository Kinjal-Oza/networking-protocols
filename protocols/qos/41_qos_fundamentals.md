# QoS (Quality of Service) Fundamentals

## Overview
Quality of Service (QoS) refers to the ability of a network to provide different levels of service to selected network traffic. It enables the network to handle specific types of traffic with different priorities, ensuring reliable delivery of critical applications and content.

## Technical Details

### QoS Models

1. **Best-Effort Service**
   - No guarantees
   - Default behavior
   - FIFO queuing
   - No traffic differentiation

2. **Integrated Services (IntServ)**
   - Resource reservation
   - End-to-end QoS
   - RSVP protocol
   - Per-flow state maintenance
   - Scalability limitations

3. **Differentiated Services (DiffServ)**
   - Class-based QoS
   - Scalable architecture
   - PHB (Per-Hop Behavior)
   - DSCP marking
   - No end-to-end guarantees

### QoS Components

1. **Classification and Marking**
   - Layer 2 (CoS/802.1p)
   - Layer 3 (IP Precedence/DSCP)
   - NBAR (Network-Based Application Recognition)
   - Access Control Lists
   - Policy maps

2. **Congestion Management**
   - FIFO (First In, First Out)
   - WFQ (Weighted Fair Queuing)
   - CBWFQ (Class-Based WFQ)
   - LLQ (Low Latency Queuing)
   - Priority Queuing

3. **Congestion Avoidance**
   - RED (Random Early Detection)
   - WRED (Weighted RED)
   - ECN (Explicit Congestion Notification)
   - Tail drop
   - Drop probability

4. **Traffic Conditioning**
   - Policing
   - Shaping
   - Rate limiting
   - Burst parameters
   - Token bucket algorithm

## Implementation

### Basic Configuration

1. **Class Map Configuration**
   ```
   ! Configure class-map
   Router(config)# class-map match-all VOICE
   Router(config-cmap)# match ip dscp ef
   Router(config-cmap)# match protocol rtp audio
   
   ! Configure class-map for video
   Router(config)# class-map match-all VIDEO
   Router(config-cmap)# match ip dscp af41
   Router(config-cmap)# match protocol rtp video
   ```

2. **Policy Map Configuration**
   ```
   ! Configure policy-map
   Router(config)# policy-map QOS-POLICY
   Router(config-pmap)# class VOICE
   Router(config-pmap-c)# priority 128
   Router(config-pmap-c)# police 128000 conform-action transmit exceed-action drop
   
   Router(config-pmap)# class VIDEO
   Router(config-pmap-c)# bandwidth percent 30
   Router(config-pmap-c)# queue-limit 90 packets
   ```

### Advanced Configuration

1. **NBAR Configuration**
   ```
   ! Enable NBAR
   Router(config)# ip nbar protocol-discovery
   Router(config-if)# ip nbar protocol-discovery
   
   ! Configure NBAR-based classification
   Router(config)# class-map match-all HTTP
   Router(config-cmap)# match protocol http url "*important*"
   ```

2. **WRED Configuration**
   ```
   ! Configure WRED
   Router(config)# random-detect
   Router(config)# random-detect dscp 46 32 64 10
   ```

## Design Considerations

### Network Planning
1. **Requirements Analysis**
   - Application needs
   - Bandwidth requirements
   - Delay sensitivity
   - Loss tolerance
   - Jitter requirements

2. **QoS Strategy**
   - End-to-end vs. per-hop
   - Classification points
   - Trust boundaries
   - Marking policies
   - Queuing strategies

3. **Capacity Planning**
   - Link utilization
   - Queue sizes
   - Buffer allocation
   - CPU resources
   - Memory requirements

### Performance Impact
1. **Resource Utilization**
   - CPU overhead
   - Memory usage
   - Buffer allocation
   - Queue processing
   - Classification load

2. **Monitoring Requirements**
   - Traffic statistics
   - Queue depths
   - Drop rates
   - Delay measurements
   - Bandwidth utilization

## Troubleshooting

### Common Issues
1. **Classification Problems**
   - Incorrect marking
   - Trust boundary issues
   - NBAR failures
   - ACL mismatches
   - Protocol recognition

2. **Queuing Issues**
   - Buffer overflow
   - Queue starvation
   - Priority queue hogging
   - Bandwidth allocation
   - Latency problems

3. **Performance Issues**
   - High CPU usage
   - Memory exhaustion
   - Packet drops
   - Excessive delays
   - Jitter variation

### Verification Commands
```
show policy-map interface
show class-map
show ip nbar protocol-discovery
show queue
show random-detect
debug qos packet
```

## Best Practices

### Design Guidelines
1. **Classification Strategy**
   - Clear traffic classes
   - Consistent marking
   - Appropriate trust
   - Simplified policies
   - Scalable design

2. **Queue Management**
   - Appropriate queue sizes
   - Balanced allocation
   - Drop thresholds
   - Buffer management
   - Priority protection

3. **Monitoring Setup**
   - Performance baselines
   - Regular monitoring
   - Threshold alerts
   - Capacity tracking
   - Trend analysis

### Security Considerations
1. **Access Control**
   ```
   ! Configure QoS ACL
   Router(config)# ip access-list extended QOS-ACL
   Router(config-ext-nacl)# permit ip any any dscp ef
   ```

2. **Trust Boundary**
   ```
   ! Configure trust boundary
   Router(config-if)# mls qos trust dscp
   Router(config-if)# service-policy input TRUST-POLICY
   ```

## Interview Tips
- Understand QoS models and their differences
- Know classification and marking mechanisms
- Explain queuing algorithms
- Understand congestion management
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Performance impact
- Real-world scenarios:
  - VoIP deployment
  - Video conferencing
  - Critical applications
  - WAN optimization
- Best practices:
  - Classification design
  - Queue management
  - Performance monitoring
  - Security integration
- Advanced concepts:
  - AutoQoS
  - NBAR
  - Hierarchical QoS
  - QoS pre-classification 