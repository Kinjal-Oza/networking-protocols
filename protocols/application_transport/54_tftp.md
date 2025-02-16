# TFTP (Trivial File Transfer Protocol)

## Overview
TFTP is a simple file transfer protocol that allows clients to get or put files to/from a remote host. It operates with minimal protocol overhead, making it ideal for network booting, configuration transfer, and firmware updates in resource-constrained environments.

## Technical Details

### Protocol Characteristics
- Application Layer Protocol
- UDP Port 69
- No authentication
- No directory services
- Maximum file size: 32 MB (RFC 2348)
- Simple request/response protocol
- Lock-step transfer process
- Block size: 512 bytes (default)

### TFTP Operations

1. **Message Types**
   ```
   RRQ   (1): Read Request
   WRQ   (2): Write Request
   DATA  (3): Data Packet
   ACK   (4): Acknowledgment
   ERROR (5): Error Message
   ```

2. **Error Codes**
   ```
   0: Not defined
   1: File not found
   2: Access violation
   3: Disk full
   4: Illegal operation
   5: Unknown transfer ID
   6: File already exists
   7: No such user
   ```

## Implementation

### TFTP Server Setup

1. **Basic Configuration (Linux)**
   ```bash
   # /etc/default/tftpd-hpa
   TFTP_USERNAME="tftp"
   TFTP_DIRECTORY="/srv/tftp"
   TFTP_ADDRESS=":69"
   TFTP_OPTIONS="--secure"
   
   # Create and secure TFTP directory
   sudo mkdir -p /srv/tftp
   sudo chown -R tftp:tftp /srv/tftp
   sudo chmod -R 755 /srv/tftp
   ```

2. **Advanced Server Configuration**
   ```bash
   # /etc/xinetd.d/tftp
   service tftp
   {
       protocol        = udp
       port           = 69
       socket_type    = dgram
       wait           = yes
       user           = root
       server         = /usr/sbin/in.tftpd
       server_args    = -s /srv/tftp -c -p
       disable        = no
       per_source     = 11
       cps           = 100 2
       flags          = IPv4
   }
   ```

### TFTP Client Implementation

1. **Python TFTP Client**
   ```python
   import tftpy

   def tftp_get(server, filename, local_path):
       try:
           client = tftpy.TftpClient(server, 69)
           client.download(filename, local_path)
           print(f"Downloaded {filename} successfully")
       except tftpy.TftpException as e:
           print(f"Error: {e}")

   def tftp_put(server, filename, remote_path):
       try:
           client = tftpy.TftpClient(server, 69)
           client.upload(remote_path, filename)
           print(f"Uploaded {filename} successfully")
       except tftpy.TftpException as e:
           print(f"Error: {e}")
   ```

2. **Error Handling**
   ```python
   class TFTPClient:
       def __init__(self, server, port=69, timeout=5):
           self.server = server
           self.port = port
           self.timeout = timeout
           
       def transfer_with_retry(self, operation, filename, max_retries=3):
           for attempt in range(max_retries):
               try:
                   client = tftpy.TftpClient(self.server, self.port)
                   if operation == 'get':
                       client.download(filename, f"local_{filename}")
                   else:
                       client.upload(filename, f"remote_{filename}")
                   return True
               except tftpy.TftpException as e:
                   print(f"Attempt {attempt + 1} failed: {e}")
                   if attempt == max_retries - 1:
                       raise
   ```

## Security Considerations

### Access Control

1. **Directory Restrictions**
   ```bash
   # Configure restricted TFTP root
   TFTP_DIRECTORY="/srv/tftp"
   TFTP_OPTIONS="--secure --create --permissive"
   
   # Set appropriate permissions
   chmod -R 755 /srv/tftp
   chown -R tftp:tftp /srv/tftp
   ```

2. **Network Security**
   ```bash
   # Configure firewall rules (iptables)
   iptables -A INPUT -p udp --dport 69 -j ACCEPT
   iptables -A INPUT -p udp --sport 69 -j ACCEPT
   
   # Restrict to specific network
   iptables -A INPUT -p udp --dport 69 -s 192.168.1.0/24 -j ACCEPT
   iptables -A INPUT -p udp --dport 69 -j DROP
   ```

## Best Practices

### Server Configuration

1. **Performance Tuning**
   ```bash
   # Optimize block size
   TFTP_OPTIONS="--secure --blocksize 1468"
   
   # Set timeout values
   TFTP_OPTIONS="--secure --timeout 5"
   
   # Limit concurrent sessions
   TFTP_OPTIONS="--secure --max-instance 10"
   ```

2. **Monitoring Setup**
   ```python
   import logging
   import time

   def monitor_tftp(log_file='/var/log/tftp_monitor.log'):
       logging.basicConfig(filename=log_file, level=logging.INFO)
       
       def log_transfer(filename, operation, status):
           timestamp = time.strftime('%Y-%m-%d %H:%M:%S')
           logging.info(f"{timestamp} - {operation} - {filename} - {status}")
       
       return log_transfer
   ```

### Client Implementation

1. **Robust Transfer**
   ```python
   def robust_transfer(server, filename, operation='get', timeout=5):
       max_block_size = 1468
       retries = 3
       
       for attempt in range(retries):
           try:
               client = tftpy.TftpClient(server, 69,
                                       options={'blksize': max_block_size})
               if operation == 'get':
                   client.download(filename, f"local_{filename}")
               else:
                   client.upload(filename, f"remote_{filename}")
               return True
           except Exception as e:
               if attempt == retries - 1:
                   raise
               time.sleep(1)
   ```

## Interview Tips
- Understand TFTP simplicity and limitations
- Know protocol operations
- Explain transfer process
- Understand security implications
- Be familiar with:
  - Server configuration
  - Client implementation
  - Error handling
  - Performance tuning
- Real-world scenarios:
  - Network boot (PXE)
  - Device configuration
  - Firmware updates
  - Backup/restore
- Best practices:
  - Security implementation
  - Performance optimization
  - Error handling
  - Monitoring setup
- Advanced concepts:
  - Block size negotiation
  - Timeout handling
  - Transfer modes
  - Access control 