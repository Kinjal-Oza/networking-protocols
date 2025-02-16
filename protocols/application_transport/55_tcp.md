# TCP (Transmission Control Protocol)

## Overview
TCP is a core transport layer protocol that provides reliable, ordered, and error-checked delivery of data streams between applications running on hosts communicating via an IP network. It is connection-oriented and forms a fundamental part of the Internet Protocol Suite.

## Technical Details

### Protocol Characteristics
- Transport Layer Protocol
- Connection-oriented
- Full-duplex communication
- Reliable data delivery
- Flow control
- Congestion control
- Ordered packet delivery
- Error detection/correction
- Segment retransmission

### Protocol Components

1. **Header Format**
   ```
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          Source Port          |       Destination Port          |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                        Sequence Number                          |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                     Acknowledgment Number                       |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   | Offset|  Res. |     Flags     |             Window             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           Checksum            |         Urgent Pointer         |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                    Options                    |    Padding      |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   ```

2. **Control Flags**
   ```
   URG: Urgent Pointer field significant
   ACK: Acknowledgment field significant
   PSH: Push Function
   RST: Reset the connection
   SYN: Synchronize sequence numbers
   FIN: No more data from sender
   ```

## Implementation

### Connection Establishment

1. **Three-Way Handshake**
   ```python
   def three_way_handshake():
       """
       TCP Three-Way Handshake Process
       """
       # Step 1: SYN
       client_seq = random.randint(0, 2**32)
       syn_segment = TCPSegment(
           flags={'SYN': 1},
           seq_num=client_seq
       )
       
       # Step 2: SYN-ACK
       server_seq = random.randint(0, 2**32)
       syn_ack_segment = TCPSegment(
           flags={'SYN': 1, 'ACK': 1},
           seq_num=server_seq,
           ack_num=client_seq + 1
       )
       
       # Step 3: ACK
       ack_segment = TCPSegment(
           flags={'ACK': 1},
           seq_num=client_seq + 1,
           ack_num=server_seq + 1
       )
   ```

2. **Connection Termination**
   ```python
   def four_way_handshake():
       """
       TCP Connection Termination Process
       """
       # Step 1: FIN from client
       fin_segment = TCPSegment(
           flags={'FIN': 1},
           seq_num=client_seq
       )
       
       # Step 2: ACK from server
       ack_segment = TCPSegment(
           flags={'ACK': 1},
           ack_num=client_seq + 1
       )
       
       # Step 3: FIN from server
       fin_segment = TCPSegment(
           flags={'FIN': 1},
           seq_num=server_seq
       )
       
       # Step 4: ACK from client
       ack_segment = TCPSegment(
           flags={'ACK': 1},
           ack_num=server_seq + 1
       )
   ```

### Flow Control

1. **Sliding Window**
   ```python
   class SlidingWindow:
       def __init__(self, window_size):
           self.window_size = window_size
           self.base = 0
           self.next_seq = 0
           self.segments = {}
           
       def send(self, data):
           if self.next_seq < self.base + self.window_size:
               self.segments[self.next_seq] = data
               self.next_seq += len(data)
               return True
           return False
           
       def receive_ack(self, ack_num):
           self.base = max(self.base, ack_num)
           # Remove acknowledged segments
           self.segments = {seq: data for seq, data in 
                          self.segments.items() if seq >= self.base}
   ```

2. **Congestion Control**
   ```python
   class CongestionControl:
       def __init__(self):
           self.cwnd = 1      # Congestion window
           self.ssthresh = 65535  # Slow start threshold
           self.state = 'slow_start'
           
       def on_ack(self):
           if self.state == 'slow_start':
               self.cwnd *= 2
               if self.cwnd >= self.ssthresh:
                   self.state = 'congestion_avoidance'
           elif self.state == 'congestion_avoidance':
               self.cwnd += 1
               
       def on_timeout(self):
           self.ssthresh = self.cwnd // 2
           self.cwnd = 1
           self.state = 'slow_start'
   ```

## Performance Optimization

### TCP Tuning

1. **Socket Buffer Sizes**
   ```bash
   # System-wide settings
   sysctl -w net.core.rmem_max=16777216
   sysctl -w net.core.wmem_max=16777216
   sysctl -w net.ipv4.tcp_rmem="4096 87380 16777216"
   sysctl -w net.ipv4.tcp_wmem="4096 87380 16777216"
   ```

2. **TCP Parameters**
   ```bash
   # Enable window scaling
   sysctl -w net.ipv4.tcp_window_scaling=1
   
   # Enable selective acknowledgments
   sysctl -w net.ipv4.tcp_sack=1
   
   # Enable timestamps
   sysctl -w net.ipv4.tcp_timestamps=1
   ```

### Monitoring

1. **Connection Statistics**
   ```python
   def monitor_tcp_stats():
       with open('/proc/net/tcp', 'r') as f:
           stats = f.readlines()
           
       connections = []
       for line in stats[1:]:  # Skip header
           fields = line.strip().split()
           connections.append({
               'local_addr': fields[1],
               'remote_addr': fields[2],
               'state': fields[3],
               'tx_queue': fields[4].split(':')[0],
               'rx_queue': fields[4].split(':')[1],
               'timer': fields[5],
               'retransmits': fields[6]
           })
       return connections
   ```

2. **Performance Metrics**
   ```python
   def get_tcp_metrics(sock):
       """Get TCP connection metrics"""
       metrics = {
           'rtt': sock.getsockopt(socket.IPPROTO_TCP, socket.TCP_INFO),
           'cwnd': sock.getsockopt(socket.IPPROTO_TCP, socket.TCP_CWND),
           'mss': sock.getsockopt(socket.IPPROTO_TCP, socket.TCP_MAXSEG),
           'retrans': sock.getsockopt(socket.IPPROTO_TCP, socket.TCP_RETRANS)
       }
       return metrics
   ```

## Best Practices

### Security Considerations

1. **SYN Flood Protection**
   ```bash
   # Enable SYN cookies
   sysctl -w net.ipv4.tcp_syncookies=1
   
   # Tune SYN backlog
   sysctl -w net.ipv4.tcp_max_syn_backlog=4096
   
   # Enable TIME-WAIT assassination protection
   sysctl -w net.ipv4.tcp_rfc1337=1
   ```

2. **Connection Timeouts**
   ```bash
   # Set keepalive parameters
   sysctl -w net.ipv4.tcp_keepalive_time=600
   sysctl -w net.ipv4.tcp_keepalive_probes=5
   sysctl -w net.ipv4.tcp_keepalive_intvl=15
   ```

### Error Handling

1. **Retransmission Strategy**
   ```python
   class RetransmissionHandler:
       def __init__(self, initial_rto=1.0):
           self.rto = initial_rto  # Retransmission timeout
           self.srtt = None        # Smoothed RTT
           self.rttvar = None      # RTT variation
           
       def update_rtt(self, measured_rtt):
           if self.srtt is None:
               self.srtt = measured_rtt
               self.rttvar = measured_rtt / 2
           else:
               self.rttvar = 0.75 * self.rttvar + 0.25 * abs(self.srtt - measured_rtt)
               self.srtt = 0.875 * self.srtt + 0.125 * measured_rtt
           
           self.rto = self.srtt + max(1, 4 * self.rttvar)
   ```

## Interview Tips
- Understand TCP's reliability mechanisms
- Know three-way handshake process
- Explain flow control and congestion control
- Understand sequence numbers and acknowledgments
- Be familiar with:
  - Header format
  - Connection states
  - Window management
  - Error recovery
- Real-world scenarios:
  - Network troubleshooting
  - Performance tuning
  - Security considerations
  - High-latency networks
- Best practices:
  - Buffer sizing
  - Timeout configuration
  - Security parameters
  - Performance monitoring
- Advanced concepts:
  - TCP options
  - Selective acknowledgments
  - Window scaling
  - Timestamps 