# FTP/SFTP (File Transfer Protocol/SSH File Transfer Protocol)

## Overview
FTP and SFTP are protocols designed for transferring files between clients and servers. While FTP is the traditional protocol operating on separate control and data channels, SFTP provides secure file transfer capabilities by operating over SSH, ensuring encrypted data transmission and authentication.

## Technical Details

### Protocol Characteristics

1. **FTP**
   - Application Layer Protocol
   - Control Channel: Port 21
   - Data Channel: Port 20 (active) or dynamic (passive)
   - Clear text protocol
   - Active and Passive modes
   - Stateful connections
   - ASCII and Binary modes

2. **SFTP**
   - SSH-based Protocol
   - Port 22 (SSH default)
   - Single encrypted channel
   - Authentication required
   - Binary transfer mode
   - Full file operations support
   - Directory operations

### Connection Modes

1. **FTP Active Mode**
   - Client opens random port > 1023
   - Server connects back on port 20
   - Client must be accessible
   - Potential firewall issues
   - NAT complications

2. **FTP Passive Mode**
   - Client initiates both connections
   - Server opens random port > 1023
   - Better firewall compatibility
   - Preferred for modern networks
   - NAT-friendly

### Commands and Responses

1. **FTP Commands**
   ```
   USER username
   PASS password
   PWD
   CWD directory
   LIST
   RETR filename
   STOR filename
   QUIT
   ```

2. **FTP Response Codes**
   ```
   1xx: Positive Preliminary reply
   2xx: Positive Completion reply
   3xx: Positive Intermediate reply
   4xx: Transient Negative Completion reply
   5xx: Permanent Negative Completion reply
   ```

## Implementation

### Basic FTP Server

1. **Python FTP Server**
   ```python
   from pyftpdlib.authorizers import DummyAuthorizer
   from pyftpdlib.handlers import FTPHandler
   from pyftpdlib.servers import FTPServer

   # Create authorizer
   authorizer = DummyAuthorizer()
   authorizer.add_user("user", "password", "/path/to/home", perm="elradfmw")

   # Create handler
   handler = FTPHandler
   handler.authorizer = authorizer

   # Create server
   server = FTPServer(("0.0.0.0", 21), handler)
   server.serve_forever()
   ```

2. **SFTP Server**
   ```python
   import paramiko

   # Generate server key
   host_key = paramiko.RSAKey.generate(2048)

   class SFTPServer(paramiko.ServerInterface):
       def check_auth_password(self, username, password):
           if username == "user" and password == "password":
               return paramiko.AUTH_SUCCESSFUL
           return paramiko.AUTH_FAILED

       def check_channel_request(self, kind, chanid):
           if kind == "session":
               return paramiko.OPEN_SUCCEEDED
           return paramiko.OPEN_FAILED_ADMINISTRATIVELY_PROHIBITED

   # Create server
   server = paramiko.Transport(("0.0.0.0", 22))
   server.add_server_key(host_key)
   server.start_server(server=SFTPServer())
   ```

### Client Implementation

1. **FTP Client**
   ```python
   from ftplib import FTP

   # Connect to server
   ftp = FTP('ftp.example.com')
   ftp.login(user='username', passwd='password')

   # Upload file
   with open('local_file.txt', 'rb') as f:
       ftp.storbinary('STOR remote_file.txt', f)

   # Download file
   with open('local_file.txt', 'wb') as f:
       ftp.retrbinary('RETR remote_file.txt', f.write)

   ftp.quit()
   ```

2. **SFTP Client**
   ```python
   import paramiko

   # Create SSH client
   ssh = paramiko.SSHClient()
   ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
   ssh.connect('sftp.example.com', username='user', password='password')

   # Create SFTP client
   sftp = ssh.open_sftp()

   # Upload file
   sftp.put('local_file.txt', 'remote_file.txt')

   # Download file
   sftp.get('remote_file.txt', 'local_file.txt')

   sftp.close()
   ssh.close()
   ```

## Security Considerations

### FTP Security

1. **Basic Security**
   ```
   # vsftpd.conf
   anonymous_enable=NO
   local_enable=YES
   write_enable=YES
   chroot_local_user=YES
   allow_writeable_chroot=NO
   ssl_enable=YES
   ```

2. **FTP over TLS (FTPS)**
   ```python
   from ftplib import FTP_TLS

   ftps = FTP_TLS('ftps.example.com')
   ftps.login(user='username', passwd='password')
   ftps.prot_p()  # Set up secure data connection
   ```

### SFTP Security

1. **SSH Configuration**
   ```
   # sshd_config
   Protocol 2
   PermitRootLogin no
   PasswordAuthentication no
   PubkeyAuthentication yes
   Subsystem sftp internal-sftp
   Match Group sftpusers
       ChrootDirectory /sftp/%u
       ForceCommand internal-sftp
   ```

2. **Key-Based Authentication**
   ```python
   import paramiko

   key = paramiko.RSAKey.from_private_key_file("/path/to/private_key")
   ssh = paramiko.SSHClient()
   ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
   ssh.connect('sftp.example.com', username='user', pkey=key)
   ```

## Best Practices

### FTP Implementation

1. **Server Configuration**
   - Use passive mode
   - Implement TLS/SSL
   - Restrict anonymous access
   - Set bandwidth limits
   - Configure firewalls
   - Monitor logs

2. **Client Configuration**
   - Use passive mode
   - Verify server certificates
   - Implement timeouts
   - Handle reconnections
   - Secure credentials
   - Log operations

### SFTP Implementation

1. **Security Measures**
   - Key-based authentication
   - Strong encryption
   - Chroot jail
   - Access controls
   - Logging
   - Monitoring

2. **Performance Optimization**
   - Buffer size tuning
   - Compression
   - Connection pooling
   - Parallel transfers
   - Error handling
   - Retry logic

## Interview Tips
- Understand FTP modes (active vs passive)
- Know SFTP security features
- Explain authentication methods
- Understand file transfer modes
- Be familiar with:
  - Protocol differences
  - Security implications
  - Performance considerations
  - Common issues
- Real-world scenarios:
  - File server setup
  - Secure transfers
  - Automation
  - Troubleshooting
- Best practices:
  - Security configuration
  - Performance tuning
  - Error handling
  - Logging
- Advanced concepts:
  - FTPS vs SFTP
  - SSH key management
  - Access control
  - Automation strategies 