# SCTP (Stream Control Transmission Protocol)

## Overview
SCTP is a transport layer protocol that combines the reliability of TCP with message-oriented features and multi-streaming capabilities. It provides ordered or unordered delivery, multi-homing support, and protection against flooding and masquerade attacks, making it ideal for telecommunications signaling and data center applications.

## Technical Details

### Protocol Characteristics
- Transport Layer Protocol
- Message-oriented delivery
- Multi-streaming support
- Multi-homing support
- Ordered/Unordered delivery
- Partial reliability option
- Four-way handshake
- Path management
- Congestion control
- Flow control

### Protocol Components

1. **Packet Format**
   ```
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                        Common Header                             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                          Chunk #1                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                           ...                                   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                          Chunk #n                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   ```

2. **Chunk Types**
   ```
   DATA          : 0  - Payload data
   INIT          : 1  - Initialization
   INIT ACK      : 2  - Initialization Acknowledgement
   SACK          : 3  - Selective Acknowledgement
   HEARTBEAT     : 4  - Heartbeat Request
   HEARTBEAT ACK : 5  - Heartbeat Acknowledgement
   ABORT         : 6  - Abort Association
   SHUTDOWN      : 7  - Shutdown Association
   SHUTDOWN ACK  : 8  - Shutdown Acknowledgement
   ERROR         : 9  - Operation Error
   COOKIE ECHO   : 10 - State Cookie
   COOKIE ACK    : 11 - Cookie Acknowledgement
   ```

## Implementation

### Association Establishment

1. **Four-Way Handshake**
   ```python
   class SCTPAssociation:
       def __init__(self):
           self.state = 'CLOSED'
           self.verification_tag = random.randint(1, 2**32)
           
       def initiate_association(self):
           """Four-way handshake process"""
           # Step 1: INIT
           init_chunk = SCTPChunk(
               type='INIT',
               flags=0,
               initiate_tag=self.verification_tag,
               a_rwnd=65535,  # Receiver window
               outbound_streams=10,
               inbound_streams=10,
               initial_tsn=random.randint(1, 2**32)
           )
           
           # Step 2: INIT ACK with Cookie
           init_ack_chunk = SCTPChunk(
               type='INIT_ACK',
               flags=0,
               initiate_tag=random.randint(1, 2**32),
               a_rwnd=65535,
               outbound_streams=10,
               inbound_streams=10,
               initial_tsn=random.randint(1, 2**32),
               state_cookie=generate_cookie()
           )
           
           # Step 3: COOKIE ECHO
           cookie_echo = SCTPChunk(
               type='COOKIE_ECHO',
               flags=0,
               cookie=init_ack_chunk.state_cookie
           )
           
           # Step 4: COOKIE ACK
           cookie_ack = SCTPChunk(
               type='COOKIE_ACK',
               flags=0
           )
           
           self.state = 'ESTABLISHED'
   ```

2. **Multi-Streaming Support**
   ```python
   class SCTPStream:
       def __init__(self, stream_id):
           self.stream_id = stream_id
           self.sequence_number = 0
           self.unordered = False
           
       def send_message(self, data, unordered=False):
           """Send message on stream"""
           chunk = SCTPChunk(
               type='DATA',
               flags=0x01 if unordered else 0,
               tsn=self.get_next_tsn(),
               stream_id=self.stream_id,
               stream_seq=self.sequence_number,
               payload=data
           )
           
           if not unordered:
               self.sequence_number += 1
               
           return chunk
   ```

### Multi-Homing Support

1. **Path Management**
   ```python
   class SCTPPathManager:
       def __init__(self):
           self.paths = {}
           self.primary_path = None
           
       def add_path(self, address):
           """Add new path to association"""
           self.paths[address] = {
               'state': 'ACTIVE',
               'cwnd': 65535,
               'ssthresh': 65535,
               'rto': 1000,  # milliseconds
               'srtt': None,
               'rttvar': None,
               'error_count': 0
           }
           
       def monitor_path(self, address):
           """Monitor path with heartbeats"""
           while True:
               try:
                   send_heartbeat(address)
                   response = wait_for_heartbeat_ack()
                   self.update_path_metrics(address, response)
               except TimeoutError:
                   self.handle_path_failure(address)
               time.sleep(30)  # Heartbeat interval
   ```

2. **Failover Handling**
   ```python
   class SCTPFailover:
       def __init__(self, path_manager):
           self.path_manager = path_manager
           
       def handle_path_failure(self, address):
           """Handle path failure and failover"""
           path = self.path_manager.paths[address]
           path['error_count'] += 1
           
           if path['error_count'] > 5:
               path['state'] = 'INACTIVE'
               
               if address == self.path_manager.primary_path:
                   self.select_new_primary_path()
                   
       def select_new_primary_path(self):
           """Select new primary path based on metrics"""
           active_paths = {addr: path for addr, path in 
                         self.path_manager.paths.items()
                         if path['state'] == 'ACTIVE'}
           
           if active_paths:
               # Select path with lowest RTT
               new_primary = min(active_paths.items(),
                               key=lambda x: x[1]['srtt'])
               self.path_manager.primary_path = new_primary[0]
   ```

## Performance Optimization

### Congestion Control

1. **Per-Path Congestion Control**
   ```python
   class SCTPCongestionControl:
       def __init__(self):
           self.cwnd = 65535
           self.ssthresh = 65535
           self.state = 'slow_start'
           
       def on_data_ack(self, bytes_acked):
           """Handle acknowledgment"""
           if self.state == 'slow_start':
               self.cwnd += min(bytes_acked, 
                              SCTP_MAX_BURST * SCTP_MTU)
               if self.cwnd >= self.ssthresh:
                   self.state = 'congestion_avoidance'
           else:
               self.cwnd += SCTP_MTU * bytes_acked / self.cwnd
               
       def on_timeout(self):
           """Handle timeout"""
           self.ssthresh = max(self.cwnd / 2, 4 * SCTP_MTU)
           self.cwnd = SCTP_MTU
           self.state = 'slow_start'
   ```

2. **Stream Priority**
   ```python
   class SCTPStreamScheduler:
       def __init__(self):
           self.streams = {}
           self.priorities = {}
           
       def add_stream(self, stream_id, priority=0):
           """Add stream with priority"""
           self.streams[stream_id] = []
           self.priorities[stream_id] = priority
           
       def schedule_transmission(self, available_space):
           """Schedule data transmission based on priority"""
           scheduled = []
           remaining_space = available_space
           
           # Sort streams by priority
           sorted_streams = sorted(self.streams.items(),
                                 key=lambda x: self.priorities[x[0]],
                                 reverse=True)
           
           for stream_id, data in sorted_streams:
               while data and remaining_space > 0:
                   chunk = data.pop(0)
                   if len(chunk) <= remaining_space:
                       scheduled.append((stream_id, chunk))
                       remaining_space -= len(chunk)
                   else:
                       data.insert(0, chunk)
                       break
                       
           return scheduled
   ```

## Best Practices

### Security Considerations

1. **Cookie Generation**
   ```python
   def generate_cookie():
       """Generate secure state cookie"""
       timestamp = int(time.time())
       random_value = os.urandom(16)
       key = get_secret_key()
       
       cookie_data = struct.pack('!I16s', timestamp, random_value)
       hmac_obj = hmac.new(key, cookie_data, hashlib.sha256)
       
       return cookie_data + hmac_obj.digest()
   ```

2. **Verification Tag Handling**
   ```python
   class SCTPVerification:
       def verify_packet(self, packet):
           """Verify SCTP packet"""
           if packet.verification_tag != self.expected_tag:
               raise SecurityError("Invalid verification tag")
               
           if not self.verify_checksum(packet):
               raise SecurityError("Invalid checksum")
               
           return True
   ```

## Interview Tips
- Understand SCTP's advantages over TCP/UDP
- Know multi-streaming concepts
- Explain multi-homing benefits
- Understand four-way handshake
- Be familiar with:
  - Message framing
  - Path management
  - Congestion control
  - Security features
- Real-world scenarios:
  - Telecommunications
  - Signaling protocols
  - Data center communications
  - High-availability systems
- Best practices:
  - Stream management
  - Path monitoring
  - Security implementation
  - Performance tuning
- Advanced concepts:
  - Partial reliability
  - Dynamic address reconfiguration
  - Stream scheduling
  - Failover mechanisms 