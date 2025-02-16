# OSI Model (Open Systems Interconnection)

## Overview
The OSI Model is a conceptual framework used to understand network interactions in seven distinct layers. It was developed by ISO to standardize network communications.

## Technical Details

### Layer Structure
1. **Physical Layer (Layer 1)**
   - Deals with physical transmission of raw bits
   - Handles voltage levels, cable specs, network adapters
   - Examples: Ethernet, USB, Bluetooth physical specifications

2. **Data Link Layer (Layer 2)**
   - Handles node-to-node communication
   - MAC addressing
   - Error detection and correction
   - Frame synchronization
   - Examples: Ethernet, PPP, IEEE 802.11 (WiFi)

3. **Network Layer (Layer 3)**
   - Manages logical addressing and routing
   - Handles packet forwarding
   - Path determination
   - Examples: IP, ICMP, OSPF

4. **Transport Layer (Layer 4)**
   - End-to-end communication
   - Segmentation and reassembly
   - Connection management
   - Examples: TCP (port 20/21), UDP (various ports)

5. **Session Layer (Layer 5)**
   - Manages sessions between applications
   - Dialog control
   - Token management
   - Examples: NetBIOS, RPC

6. **Presentation Layer (Layer 6)**
   - Data translation and encryption
   - Character encoding
   - Data compression
   - Examples: SSL/TLS, JPEG, ASCII/Unicode

7. **Application Layer (Layer 7)**
   - User interface and application services
   - Examples: HTTP(S), FTP, SMTP, DNS

### Protocol Data Units (PDUs)
- Layer 1: Bits
- Layer 2: Frames
- Layer 3: Packets
- Layer 4: Segments
- Layer 5-7: Data

## Key Concepts

### Encapsulation
- Data is wrapped with headers as it moves down the layers
- Each layer adds its own header information
- Process: Data → Segments → Packets → Frames → Bits

### Decapsulation
- Reverse process of encapsulation
- Headers are removed as data moves up the layers
- Process: Bits → Frames → Packets → Segments → Data

## Troubleshooting Approach
1. Start from Physical Layer (bottom-up approach)
2. Verify each layer's functionality
3. Use layer-specific tools:
   - Layer 1: Cable testers, multimeters
   - Layer 2: MAC address analysis
   - Layer 3: ping, traceroute
   - Layer 4: netstat, port scanning
   - Layer 7: application logs

## Interview Tips
- Understand the interaction between layers
- Know common protocols at each layer
- Be able to explain encapsulation/decapsulation
- Familiar with troubleshooting methods at each layer
- Understand why layered approach is beneficial
  - Modularity
  - Standardization
  - Easier troubleshooting
  - Independent layer development 