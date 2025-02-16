# IntServ (Integrated Services)

## Overview
Integrated Services (IntServ) is a QoS architecture that provides end-to-end service guarantees for applications by reserving network resources in advance. It uses RSVP (Resource Reservation Protocol) to signal resource requirements and establish paths through the network.

## Technical Details

### Protocol Characteristics
- Layer: 3 (Network)
- RFC: 1633, 2205 (RSVP)
- Resource Reservation: Per-flow
- Signaling Protocol: RSVP
- End-to-end QoS
- Admission Control

### Service Classes

1. **Guaranteed Service (GS)**
   - RFC: 2212
   - Strict delay bounds
   - Zero packet loss
   - Bandwidth guarantee
   - Deterministic service
   - Used for real-time apps

2. **Controlled Load Service (CLS)**
   - RFC: 2211
   - Better than best-effort
   - Low delay and loss
   - Soft guarantees
   - No strict bounds
   - Used for adaptive apps

3. **Best Effort Service**
   - No guarantees
   - Default service
   - No reservation
   - No admission control
   - Used for standard traffic

### Resource Requirements

1. **Flow Specifications**
   - Token Bucket Parameters
   - Peak Rate
   - Minimum Policed Unit
   - Maximum Packet Size
   - Rate Specifications

2. **Resource Types**
   - Bandwidth
   - Buffer Space
   - CPU Processing
   - Queue Management
   - Link Capacity

## Implementation

### Basic Configuration

1. **Enable RSVP**
   ```
   ! Enable RSVP globally
   Router(config)# ip rsvp
   
   ! Enable RSVP on interface
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# ip rsvp bandwidth 1000
   ```

2. **Service Policy**
   ```
   ! Configure service policy
   Router(config)# class-map match-all GUARANTEED
   Router(config-cmap)# match ip dscp ef
   
   Router(config)# policy-map INTSERV-POLICY
   Router(config-pmap)# class GUARANTEED
   Router(config-pmap-c)# priority 1000
   Router(config-pmap-c)# police 1000000
   ```

### Advanced Configuration

1. **Admission Control**
   ```
   ! Configure admission control
   Router(config-if)# ip rsvp admission-control 
   Router(config-if)# ip rsvp admission-control rate-limit 500
   Router(config-if)# ip rsvp admission-control ack-wait-time 4
   ```

2. **Flow Aggregation**
   ```
   ! Configure flow aggregation
   Router(config)# ip rsvp aggregation
   Router(config)# ip rsvp aggregation ip
   Router(config)# ip rsvp aggregation ip map access-list 1
   ```

## Design Considerations

### Network Planning
1. **Resource Allocation**
   - Bandwidth planning
   - Buffer allocation
   - Processing capacity
   - Queue management
   - Admission limits

2. **Scalability Concerns**
   - Number of flows
   - State maintenance
   - Processing overhead
   - Memory requirements
   - Signaling load

3. **Service Level Planning**
   - Application requirements
   - Traffic profiles
   - Guarantee levels
   - Admission policies
   - Resource limits

### Performance Impact
1. **Processing Overhead**
   - RSVP processing
   - State maintenance
   - Admission control
   - Policing
   - Classification

2. **Resource Consumption**
   - Memory usage
   - CPU utilization
   - Buffer space
   - Queue resources
   - Link capacity

## Troubleshooting

### Common Issues
1. **Reservation Problems**
   - Setup failures
   - Resource unavailability
   - Path changes
   - State timeout
   - Configuration errors

2. **Performance Issues**
   - High CPU usage
   - Memory exhaustion
   - Queue buildup
   - Packet drops
   - Excessive delay

3. **Scaling Problems**
   - Too many flows
   - State overflow
   - Processing bottlenecks
   - Resource exhaustion
   - Signaling storms

### Verification Commands
```
show ip rsvp interface
show ip rsvp reservation
show ip rsvp sender
show ip rsvp installed
show ip rsvp neighbor
debug ip rsvp
```

## Best Practices

### Design Guidelines
1. **Resource Management**
   - Conservative allocation
   - Buffer planning
   - Processing limits
   - State constraints
   - Admission thresholds

2. **Scalability Planning**
   - Flow aggregation
   - State limitation
   - Resource pools
   - Processing distribution
   - Memory management

3. **Service Policies**
   - Clear requirements
   - Admission rules
   - Update procedures
   - Monitoring plan
   - Documentation

### Security Considerations
1. **Authentication**
   ```
   ! Configure RSVP authentication
   Router(config-if)# ip rsvp authentication
   Router(config-if)# ip rsvp authentication key-chain RSVP-KEY
   
   ! Configure key chain
   Router(config)# key chain RSVP-KEY
   Router(config-keychain)# key 1
   Router(config-keychain-key)# key-string SECURE_KEY
   ```

2. **Access Control**
   ```
   ! Configure RSVP access list
   Router(config)# ip rsvp sender-host access-list 100
   Router(config)# access-list 100 permit ip any any
   ```

## Interview Tips
- Understand IntServ architecture
- Know service classes
- Explain resource reservation
- Understand RSVP operation
- Be familiar with:
  - Configuration options
  - Troubleshooting methods
  - Design principles
  - Security features
- Real-world scenarios:
  - Voice/video services
  - Critical applications
  - Multimedia streaming
  - Real-time control
- Best practices:
  - Resource planning
  - Admission control
  - State management
  - Security implementation
- Advanced concepts:
  - Flow aggregation
  - Policy control
  - RSVP extensions
  - Service templates 