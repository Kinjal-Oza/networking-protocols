# QoS Marking and Classification

## Overview
QoS Marking and Classification are foundational mechanisms that identify and label traffic for differentiated treatment in the network. Classification identifies traffic types, while marking assigns appropriate QoS values to ensure proper handling throughout the network path.

## Technical Details

### Classification Methods

1. **Layer 2 Classification**
   - CoS (Class of Service)
   - 802.1p priority
   - Frame Relay DE bit
   - ATM CLP bit
   - MPLS EXP bits

2. **Layer 3 Classification**
   - IP Precedence (0-7)
   - DSCP (0-63)
   - ToS byte
   - Flow labels
   - Traffic class

3. **Application Classification**
   - Protocol type
   - Port numbers
   - NBAR2 (Network Based Application Recognition)
   - Deep packet inspection
   - URL classification

### Marking Fields

1. **Layer 2 Marking**
   - CoS (3 bits)
   - DE bit (1 bit)
   - CLP bit (1 bit)
   - MPLS EXP (3 bits)
   - PCP (3 bits)

2. **Layer 3 Marking**
   - DSCP (6 bits)
   - IP Precedence (3 bits)
   - ECN (2 bits)
   - Traffic class (8 bits)
   - Flow label (20 bits)

## Implementation

### Basic Configuration

1. **Class Map Configuration**
   ```
   ! Configure class map for voice traffic
   Router(config)# class-map match-all VOICE
   Router(config-cmap)# match protocol voip
   Router(config-cmap)# match dscp ef
   
   ! Configure class map for video
   Router(config)# class-map match-any VIDEO
   Router(config-cmap)# match protocol telepresence
   Router(config-cmap)# match protocol webex-video
   ```

2. **Policy Map Configuration**
   ```
   ! Configure marking policy
   Router(config)# policy-map MARKING-POLICY
   Router(config-pmap)# class VOICE
   Router(config-pmap-c)# set dscp ef
   Router(config-pmap)# class VIDEO
   Router(config-pmap-c)# set dscp af41
   ```

### Advanced Configuration

1. **NBAR2 Configuration**
   ```
   ! Enable NBAR2
   Router(config)# ip nbar protocol-discovery
   
   ! Configure NBAR2 custom protocol
   Router(config)# ip nbar custom CUSTOM-APP tcp destination port-range 8000 8100
   
   ! Apply to class map
   Router(config)# class-map match-all CUSTOM-TRAFFIC
   Router(config-cmap)# match protocol CUSTOM-APP
   ```

2. **Hierarchical Marking**
   ```
   ! Configure parent policy
   Router(config)# policy-map PARENT-POLICY
   Router(config-pmap)# class class-default
   Router(config-pmap-c)# shape average 100000000
   Router(config-pmap-c)# service-policy MARKING-POLICY
   ```

## Design Considerations

### Classification Strategy
1. **Traffic Identification**
   - Application types
   - Protocol behavior
   - Business requirements
   - Network capabilities
   - Performance needs

2. **Marking Scheme**
   - End-to-end consistency
   - Trust boundaries
   - Marking preservation
   - QoS domains
   - Service provider requirements

3. **Implementation Approach**
   - Classification points
   - Marking locations
   - Trust boundaries
   - Policy enforcement
   - Monitoring requirements

### Performance Impact
1. **Processing Overhead**
   - Classification complexity
   - Deep packet inspection
   - NBAR processing
   - Policy evaluation
   - Marking operations

2. **Scalability Factors**
   - Number of classes
   - Policy complexity
   - Traffic volume
   - Interface count
   - Monitoring load

## Troubleshooting

### Common Issues
1. **Classification Problems**
   - Misidentified traffic
   - Protocol recognition
   - NBAR failures
   - Policy conflicts
   - Trust boundary issues

2. **Marking Problems**
   - Incorrect marking
   - Mark preservation
   - Policy conflicts
   - Interface compatibility
   - QoS domain issues

3. **Performance Issues**
   - High CPU usage
   - Processing delays
   - Policy complexity
   - Scale limitations
   - Resource exhaustion

### Verification Commands
```
show class-map
show policy-map
show policy-map interface
show ip nbar protocol-discovery
show ip nbar protocol-pack
debug ip nbar
show mls qos
```

## Best Practices

### Design Guidelines
1. **Classification Design**
   - Clear traffic categories
   - Consistent identification
   - Efficient matching
   - Scalable policies
   - Future flexibility

2. **Marking Strategy**
   - Standard markings
   - Domain consistency
   - Trust boundaries
   - Policy enforcement
   - Mark preservation

3. **Implementation Plan**
   - Phased deployment
   - Testing methodology
   - Verification steps
   - Documentation
   - Monitoring strategy

### Security Considerations
1. **Trust Boundary**
   ```
   ! Configure trust boundary
   Router(config)# interface GigabitEthernet0/1
   Router(config-if)# mls qos trust dscp
   Router(config-if)# service-policy input MARKING-POLICY
   ```

2. **Marking Protection**
   ```
   ! Configure marking protection
   Router(config)# ip access-list extended PROTECT-MARKING
   Router(config-ext-nacl)# deny ip any any dscp ef
   Router(config-ext-nacl)# permit ip any any
   ```

## Interview Tips
- Understand classification methods
- Know marking fields and values
- Explain trust boundaries
- Understand NBAR operation
- Be familiar with:
  - Classification tools
  - Marking schemes
  - Policy configuration
  - Troubleshooting methods
- Real-world scenarios:
  - Enterprise QoS
  - Service provider marking
  - Application classification
  - End-to-end QoS
- Best practices:
  - Classification design
  - Marking strategy
  - Policy implementation
  - Performance optimization
- Advanced concepts:
  - NBAR2 features
  - Hierarchical policies
  - Custom protocols
  - QoS domains 