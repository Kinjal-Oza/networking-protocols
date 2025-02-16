# DiffServ (Differentiated Services)

## Overview
DiffServ is a computer networking architecture that specifies a simple and scalable mechanism for classifying and managing network traffic. It provides QoS by dividing traffic into different classes and handling each class differently, particularly during congestion.

## Technical Details

### Protocol Characteristics
- Layer: 3 (Network)
- RFC: 2474, 2475
- Field: DS Field (formerly ToS)
- DSCP: 6 bits
- ECN: 2 bits
- Per-Hop Behavior (PHB)

### DSCP Values

1. **Default PHB (Best Effort)**
   - DSCP: 000000 (0)
   - Traditional IP precedence: 0
   - Standard traffic

2. **Expedited Forwarding (EF)**
   - DSCP: 101110 (46)
   - Low latency
   - Low jitter
   - Assured bandwidth
   - Used for voice

3. **Assured Forwarding (AF)**
   - Four classes (AF1x - AF4x)
   - Three drop precedences per class
   - Format: AFxy (x=class, y=drop precedence)
   ```
   Class 1: AF11(10), AF12(12), AF13(14)
   Class 2: AF21(18), AF22(20), AF23(22)
   Class 3: AF31(26), AF32(28), AF33(30)
   Class 4: AF41(34), AF42(36), AF43(38)
   ```

4. **Class Selector PHB**
   - Compatible with IP precedence
   - Eight classes (CS0-CS7)
   - DSCP values: xxx000

## Implementation

### Basic Configuration

1. **DSCP Marking**
   ```
   ! Configure class map
   Router(config)# class-map match-all VOICE
   Router(config-cmap)# match protocol voip
   
   ! Configure policy map
   Router(config)# policy-map MARK-TRAFFIC
   Router(config-pmap)# class VOICE
   Router(config-pmap-c)# set ip dscp ef
   ```

2. **PHB Configuration**
   ```
   ! Configure queuing for EF traffic
   Router(config)# policy-map QOS-POLICY
   Router(config-pmap)# class VOICE
   Router(config-pmap-c)# priority percent 10
   Router(config-pmap-c)# police 128000 conform-action transmit exceed-action drop
   ```

### Advanced Configuration

1. **AF Class Configuration**
   ```
   ! Configure AF classes
   Router(config)# class-map match-all AF41-TRAFFIC
   Router(config-cmap)# match dscp af41
   
   Router(config)# policy-map AF-POLICY
   Router(config-pmap)# class AF41-TRAFFIC
   Router(config-pmap-c)# bandwidth percent 20
   Router(config-pmap-c)# random-detect dscp-based
   ```

2. **Trust Boundary**
   ```
   ! Configure trust boundary
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# mls qos trust dscp
   Router(config-if)# service-policy input MARK-TRAFFIC
   ```

## Design Considerations

### Network Planning
1. **Traffic Classification**
   - Application requirements
   - Business priorities
   - Network capabilities
   - Bandwidth allocation
   - Drop priorities

2. **PHB Design**
   - Queue assignments
   - Bandwidth guarantees
   - Drop thresholds
   - Buffer allocation
   - Processing priorities

3. **Scalability**
   - Number of classes
   - Resource requirements
   - Management overhead
   - Network size
   - Traffic volume

### Performance Impact
1. **Resource Requirements**
   - Classification overhead
   - Queue processing
   - Buffer management
   - CPU utilization
   - Memory usage

2. **Network Effects**
   - End-to-end delay
   - Jitter control
   - Packet loss
   - Throughput
   - Link utilization

## Troubleshooting

### Common Issues
1. **Marking Problems**
   - Incorrect DSCP values
   - Trust boundary issues
   - Classification errors
   - Policy conflicts
   - Configuration mistakes

2. **PHB Issues**
   - Queue starvation
   - Bandwidth hogging
   - Drop threshold problems
   - Buffer overflow
   - Processing delays

3. **End-to-End Issues**
   - PHB inconsistency
   - Path changes
   - Provider compliance
   - Inter-domain behavior
   - Service level violations

### Verification Commands
```
show mls qos
show mls qos interface
show policy-map interface
show class-map
show mls qos maps dscp-output-q
debug qos marking
```

## Best Practices

### Design Guidelines
1. **Classification Strategy**
   - Limited number of classes
   - Clear business alignment
   - Consistent marking
   - End-to-end planning
   - Regular review

2. **PHB Implementation**
   - Appropriate bandwidth allocation
   - Conservative drop thresholds
   - Buffer management
   - Queue protection
   - Processing priorities

3. **Monitoring and Management**
   - Performance monitoring
   - SLA verification
   - Capacity planning
   - Change control
   - Documentation

### Security Considerations
1. **Trust Boundary**
   ```
   ! Configure extended trust boundary
   Router(config)# interface range GigabitEthernet0/1 - 24
   Router(config-if-range)# mls qos trust dscp
   Router(config-if-range)# service-policy input MARK-TRAFFIC
   ```

2. **Marking Protection**
   ```
   ! Configure marking protection
   Router(config)# ip access-list extended PROTECT-MARKING
   Router(config-ext-nacl)# deny ip any any dscp ef
   Router(config-ext-nacl)# permit ip any any
   ```

## Interview Tips
- Understand DiffServ architecture
- Know DSCP values and classes
- Explain PHB concepts
- Understand AF classes
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security implications
- Real-world scenarios:
  - Enterprise QoS
  - Service provider networks
  - Cloud services
  - VoIP implementation
- Best practices:
  - Classification design
  - PHB implementation
  - Monitoring setup
  - Security integration
- Advanced concepts:
  - DiffServ domains
  - Traffic conditioning
  - PHB groups
  - Inter-domain behavior 