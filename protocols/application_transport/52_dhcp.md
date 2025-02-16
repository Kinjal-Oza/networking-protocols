# DHCP (Dynamic Host Configuration Protocol)

## Overview
DHCP is a network management protocol used to dynamically assign IP addresses and other network configuration parameters to devices on a network. It automates and centralizes network configuration, reducing administrative overhead and configuration errors.

## Technical Details

### Protocol Characteristics
- Application Layer Protocol
- UDP Ports: 67 (Server), 68 (Client)
- Broadcast-based discovery
- Lease-based allocation
- Dynamic/Static assignment
- Hierarchical configuration
- Relay agent support

### DHCP Process

1. **DORA Process**
   ```
   DHCP Discover → Broadcast (Client)
   DHCP Offer    → Unicast/Broadcast (Server)
   DHCP Request  → Broadcast (Client)
   DHCP Ack      → Unicast/Broadcast (Server)
   ```

2. **Message Types**
   ```
   1  - DHCPDISCOVER
   2  - DHCPOFFER
   3  - DHCPREQUEST
   4  - DHCPDECLINE
   5  - DHCPACK
   6  - DHCPNAK
   7  - DHCPRELEASE
   8  - DHCPINFORM
   ```

## Implementation

### DHCP Server Setup

1. **ISC DHCP Server Configuration**
   ```bash
   # /etc/dhcp/dhcpd.conf
   subnet 192.168.1.0 netmask 255.255.255.0 {
       range 192.168.1.100 192.168.1.200;
       option routers 192.168.1.1;
       option domain-name-servers 8.8.8.8, 8.8.4.4;
       option domain-name "example.com";
       default-lease-time 86400;
       max-lease-time 172800;
   }

   # Static assignment
   host printer {
       hardware ethernet 00:11:22:33:44:55;
       fixed-address 192.168.1.10;
   }
   ```

2. **DHCP Pools and Scopes**
   ```bash
   # Multiple subnets
   shared-network "office" {
       subnet 192.168.1.0 netmask 255.255.255.0 {
           range 192.168.1.100 192.168.1.200;
           option routers 192.168.1.1;
       }
       
       subnet 192.168.2.0 netmask 255.255.255.0 {
           range 192.168.2.100 192.168.2.200;
           option routers 192.168.2.1;
       }
   }
   ```

### DHCP Client Implementation

1. **Python DHCP Client**
   ```python
   import socket
   import struct

   def create_dhcp_discover():
       # DHCP Message Format
       message = struct.pack('!BBBBL4s4s4s4s6s10s192s28s4s',
           1,      # Message type (1 for discover)
           1,      # Hardware type (1 for Ethernet)
           6,      # Hardware address length
           0,      # Hops
           0,      # Transaction ID
           b'\x00' * 4,  # Seconds
           b'\x00' * 4,  # Flags
           b'\x00' * 4,  # Client IP
           b'\x00' * 4,  # Your IP
           b'\x00' * 6,  # Server MAC
           b'\x00' * 10, # Server hostname
           b'\x00' * 192,# Boot filename
           b'\x63\x82\x53\x63',  # Magic cookie
           b'\x35\x01\x01'       # DHCP Discover option
       )
       return message

   def send_dhcp_discover():
       sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
       sock.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, 1)
       sock.bind(('0.0.0.0', 68))
       sock.sendto(create_dhcp_discover(), ('255.255.255.255', 67))
   ```

2. **DHCP Lease Management**
   ```python
   import time
   from datetime import datetime

   class DHCPLease:
       def __init__(self, ip_address, lease_time):
           self.ip_address = ip_address
           self.lease_time = lease_time
           self.start_time = time.time()
           
       def is_valid(self):
           elapsed = time.time() - self.start_time
           return elapsed < self.lease_time
           
       def time_remaining(self):
           elapsed = time.time() - self.start_time
           return max(0, self.lease_time - elapsed)
           
       def renew(self):
           if self.time_remaining() < (self.lease_time / 2):
               # Implement DHCP renew request
               pass
   ```

## Security Considerations

### DHCP Snooping

1. **Switch Configuration**
   ```
   ! Enable DHCP snooping
   Switch(config)# ip dhcp snooping
   Switch(config)# ip dhcp snooping vlan 1-100
   
   ! Configure trusted ports
   Switch(config)# interface GigabitEthernet0/1
   Switch(config-if)# ip dhcp snooping trust
   
   ! Rate limiting
   Switch(config-if)# ip dhcp snooping limit rate 100
   ```

2. **DHCP Option 82**
   ```bash
   # dhcpd.conf
   class "option-82" {
       match if exists dhcp-agent-options;
       option agent.circuit-id "circuit-id";
       option agent.remote-id "remote-id";
   }
   ```

### Access Control

1. **MAC Filtering**
   ```bash
   # dhcpd.conf
   deny unknown-clients;
   
   host allowed-device {
       hardware ethernet 00:11:22:33:44:55;
       fixed-address 192.168.1.100;
   }
   ```

2. **Subnet Restrictions**
   ```bash
   # dhcpd.conf
   subnet 192.168.1.0 netmask 255.255.255.0 {
       deny unknown-clients;
       pool {
           allow members of "trusted-devices";
           range 192.168.1.100 192.168.1.200;
       }
   }
   ```

## Best Practices

### Server Configuration

1. **High Availability**
   ```bash
   # Primary Server
   failover peer "dhcp-failover" {
       primary;
       address 192.168.1.10;
       port 647;
       peer address 192.168.1.11;
       peer port 647;
       max-response-delay 30;
       max-unacked-updates 10;
       load balance max seconds 3;
   }
   ```

2. **Logging Configuration**
   ```bash
   # dhcpd.conf
   log-facility local7;
   
   # syslog configuration
   local7.* /var/log/dhcp.log
   ```

### Network Design

1. **Scope Planning**
   ```bash
   # Separate scopes for different device types
   subnet 192.168.1.0 netmask 255.255.255.0 {
       # Workstations
       pool {
           range 192.168.1.100 192.168.1.150;
           allow members of "workstations";
       }
       
       # VoIP phones
       pool {
           range 192.168.1.151 192.168.1.200;
           allow members of "voip-devices";
           option voip-tftp-server 192.168.1.10;
       }
   }
   ```

2. **Performance Optimization**
   ```bash
   # dhcpd.conf
   ddns-update-style none;
   authoritative;
   ping-check true;
   lease-file-name "/var/lib/dhcp/dhcpd.leases";
   ```

## Interview Tips
- Understand DHCP operation
- Know message types and process
- Explain lease lifecycle
- Understand security features
- Be familiar with:
  - Server configuration
  - Client implementation
  - Security measures
  - Troubleshooting
- Real-world scenarios:
  - Enterprise deployment
  - VOIP implementation
  - Wireless networks
  - Multi-subnet setup
- Best practices:
  - Scope design
  - Security implementation
  - High availability
  - Performance tuning
- Advanced concepts:
  - DHCP failover
  - Option 82
  - DHCP snooping
  - Dynamic VLAN assignment 