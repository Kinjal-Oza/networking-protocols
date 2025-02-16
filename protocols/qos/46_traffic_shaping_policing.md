# Traffic Shaping and Policing

## Overview
Traffic Shaping and Policing are QoS mechanisms used to control traffic rates in networks. While both manage traffic flow, they use different approaches: shaping buffers excess traffic for later transmission, while policing drops or remarks excess traffic.

## Technical Details

### Characteristics

1. **Traffic Shaping**
   - Buffers excess traffic
   - Smooths traffic flow
   - Delays excess packets
   - Maintains burst size
   - Token bucket algorithm

2. **Traffic Policing**
   - Drops/remarks excess
   - No buffering
   - Immediate action
   - Enforces hard limits
   - Token bucket algorithm

### Key Parameters

1. **Common Parameters**
   - Committed Information Rate (CIR)
   - Committed Burst Size (CBS)
   - Excess Burst Size (EBS)
   - Peak Information Rate (PIR)
   - Measurement interval

2. **Token Bucket Components**
   - Bucket depth
   - Token generation rate
   - Token consumption
   - Conforming traffic
   - Exceeding traffic

## Implementation

### Traffic Shaping Configuration

1. **Generic Traffic Shaping**
   ```
   ! Configure traffic shaping
   Router(config)# interface Serial0/0
   Router(config-if)# traffic-shape rate 512000
   Router(config-if)# traffic-shape burst 32000
   Router(config-if)# traffic-shape adaptive 384000
   ```

2. **Class-Based Shaping**
   ```
   ! Configure class map
   Router(config)# class-map match-all SHAPE-CLASS
   Router(config-cmap)# match access-group 101
   
   ! Configure policy map
   Router(config)# policy-map SHAPE-POLICY
   Router(config-pmap)# class SHAPE-CLASS
   Router(config-pmap-c)# shape average 512000 32000
   ```

### Traffic Policing Configuration

1. **Interface-Based Policing**
   ```
   ! Configure traffic policing
   Router(config)# interface FastEthernet0/0
   Router(config-if)# service-policy input POLICE-POLICY
   
   ! Configure policy
   Router(config)# policy-map POLICE-POLICY
   Router(config-pmap)# class CLASS-DEFAULT
   Router(config-pmap-c)# police 512000 8000 exceed-action drop
   ```

2. **Two-Rate Three-Color Policing**
   ```
   ! Configure policer
   Router(config)# policy-map TWO-RATE-POLICY
   Router(config-pmap)# class CRITICAL-TRAFFIC
   Router(config-pmap-c)# police cir 512000 pir 1024000
   Router(config-pmap-c-police)# conform-action transmit
   Router(config-pmap-c-police)# exceed-action set-dscp-transmit af21
   Router(config-pmap-c-police)# violate-action drop
   ```

## Design Considerations

### Traffic Management Planning
1. **Application Requirements**
   - Bandwidth needs
   - Burst tolerance
   - Delay sensitivity
   - Drop preferences
   - Priority levels

2. **Network Capabilities**
   - Link capacities
   - Buffer availability
   - Processing power
   - Memory resources
   - QoS support

3. **Implementation Strategy**
   - Shaping vs Policing
   - Rate selection
   - Burst parameters
   - Action policies
   - Monitoring requirements

### Performance Impact
1. **Shaping Considerations**
   - Added delay
   - Buffer requirements
   - CPU overhead
   - Memory usage
   - Queue management

2. **Policing Considerations**
   - Packet drops
   - Application impact
   - Retransmission effects
   - Service levels
   - Network stability

## Troubleshooting

### Common Issues
1. **Shaping Problems**
   - Buffer overflow
   - Excessive delay
   - Rate misconfiguration
   - Burst size issues
   - Queue buildup

2. **Policing Problems**
   - Excessive drops
   - Rate mismatch
   - Burst violations
   - Application failures
   - Performance degradation

3. **Configuration Issues**
   - Parameter errors
   - Policy misapplication
   - Class matching
   - Action conflicts
   - Interface compatibility

### Verification Commands
```
show policy-map interface
show traffic-shape statistics
show interface rate-limit
debug traffic-shape events
show platform qos policy-map
show policy-map statistics
```

## Best Practices

### Design Guidelines
1. **Rate Selection**
   - Conservative limits
   - Appropriate bursts
   - Application awareness
   - Growth planning
   - Link utilization

2. **Implementation Strategy**
   - Clear policies
   - Consistent configuration
   - Proper monitoring
   - Regular review
   - Documentation

3. **Performance Optimization**
   - Buffer tuning
   - Rate adjustment
   - Burst optimization
   - Action refinement
   - Resource management

### Security Considerations
1. **Traffic Protection**
   ```
   ! Configure traffic protection
   Router(config)# policy-map PROTECT-TRAFFIC
   Router(config-pmap)# class HIGH-PRIORITY
   Router(config-pmap-c)# police cir 1000000 conform-action transmit exceed-action drop
   ```

2. **Resource Protection**
   ```
   ! Configure resource limits
   Router(config)# platform qos police rate-limit 20
   Router(config)# platform qos shape rate-limit 1024000
   ```

## Interview Tips
- Understand shaping vs policing
- Know configuration options
- Explain token bucket algorithm
- Understand performance impact
- Be familiar with:
  - Rate parameters
  - Burst settings
  - Action policies
  - Monitoring tools
- Real-world scenarios:
  - WAN optimization
  - Service provider networks
  - Enterprise QoS
  - Cloud services
- Best practices:
  - Rate selection
  - Burst configuration
  - Action planning
  - Resource management
- Advanced concepts:
  - Hierarchical policies
  - Color-aware policing
  - Adaptive shaping
  - QoS integration 