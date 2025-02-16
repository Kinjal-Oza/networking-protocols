# Nagios

## Overview
Nagios is an open-source monitoring system that enables organizations to identify and resolve IT infrastructure problems before they affect critical business processes. It provides monitoring of network services, host resources, and server components while offering a robust notification system for alerting when problems occur.

## Technical Details

### Architecture Components

1. **Core Components**
   - Nagios Core Engine
   - Configuration Files
   - Plugins (Check Commands)
   - Web Interface
   - State Retention
   - Event Handlers
   - Performance Data

2. **Monitoring Capabilities**
   - Network Services (SMTP, POP3, HTTP, NNTP, ICMP, SNMP)
   - Host Resources (CPU load, disk usage, memory usage)
   - System Metrics (processor load, disk usage)
   - Network Protocols
   - Applications
   - Services
   - Operating Systems

3. **Plugin Architecture**
   - Standard Plugins
   - NRPE (Nagios Remote Plugin Executor)
   - NSCA (Nagios Service Check Acceptor)
   - Custom Plugins
   - Check_MK
   - Third-party Plugins

## Implementation

### Basic Configuration

1. **Main Configuration (nagios.cfg)**
   ```ini
   # Basic Settings
   log_file=/usr/local/nagios/var/nagios.log
   cfg_file=/usr/local/nagios/etc/objects/commands.cfg
   cfg_file=/usr/local/nagios/etc/objects/contacts.cfg
   cfg_file=/usr/local/nagios/etc/objects/timeperiods.cfg
   cfg_file=/usr/local/nagios/etc/objects/templates.cfg
   
   # State Retention
   retain_state_information=1
   state_retention_file=/usr/local/nagios/var/retention.dat
   
   # Performance Data
   process_performance_data=1
   host_perfdata_file=/usr/local/nagios/var/host-perfdata
   service_perfdata_file=/usr/local/nagios/var/service-perfdata
   ```

2. **Host Definition**
   ```cfg
   define host {
       use                     linux-server
       host_name               web-server
       alias                   Web Server
       address                 192.168.1.10
       max_check_attempts      5
       check_period           24x7
       notification_interval   30
       notification_period    24x7
       contacts               admin
   }
   ```

3. **Service Definition**
   ```cfg
   define service {
       use                    generic-service
       host_name              web-server
       service_description    HTTP
       check_command         check_http
       max_check_attempts    3
       check_interval        5
       retry_interval        1
       notification_interval 30
       notification_period   24x7
       contacts             admin
   }
   ```

### Advanced Configuration

1. **Command Definition**
   ```cfg
   define command {
       command_name check_disk
       command_line $USER1$/check_disk -w $ARG1$ -c $ARG2$ -p $ARG3$
   }
   
   define command {
       command_name check_load
       command_line $USER1$/check_load -w $ARG1$ -c $ARG2$
   }
   ```

2. **Contact Configuration**
   ```cfg
   define contact {
       contact_name                    admin
       alias                          Administrator
       service_notification_period     24x7
       host_notification_period       24x7
       service_notification_options    w,u,c,r
       host_notification_options      d,u,r
       service_notification_commands   notify-service-by-email
       host_notification_commands     notify-host-by-email
       email                         admin@example.com
   }
   ```

### Plugin Development

1. **Custom Check Plugin (Python)**
   ```python
   #!/usr/bin/env python3
   import sys
   import argparse
   
   def check_resource(warning_threshold, critical_threshold):
       # Implement check logic here
       current_value = get_resource_value()
       
       if current_value >= critical_threshold:
           print(f"CRITICAL - Resource value: {current_value}")
           return 2
       elif current_value >= warning_threshold:
           print(f"WARNING - Resource value: {current_value}")
           return 1
       else:
           print(f"OK - Resource value: {current_value}")
           return 0
   
   def main():
       parser = argparse.ArgumentParser()
       parser.add_argument('-w', '--warning', type=float, required=True)
       parser.add_argument('-c', '--critical', type=float, required=True)
       args = parser.parse_args()
       
       sys.exit(check_resource(args.warning, args.critical))
   
   if __name__ == '__main__':
       main()
   ```

2. **NRPE Configuration**
   ```ini
   # /etc/nagios/nrpe.cfg
   server_address=0.0.0.0
   server_port=5666
   allowed_hosts=127.0.0.1,192.168.1.1
   dont_blame_nrpe=1
   
   command[check_users]=/usr/lib/nagios/plugins/check_users -w 5 -c 10
   command[check_load]=/usr/lib/nagios/plugins/check_load -w 15,10,5 -c 30,25,20
   command[check_disk]=/usr/lib/nagios/plugins/check_disk -w 20% -c 10% -p /
   ```

## Performance Optimization

### Resource Management

1. **Service Check Optimization**
   ```cfg
   # Tune check intervals
   service_check_timeout=60
   max_concurrent_checks=0
   check_result_reaper_frequency=10
   max_check_result_reaper_time=30
   
   # Enable cached checks
   use_cached_checks=1
   cached_host_check_horizon=15
   cached_service_check_horizon=15
   ```

2. **Memory Management**
   ```cfg
   # Buffer settings
   buffer_length=8192
   max_buffer_length=65536
   
   # Process settings
   check_workers=4
   ```

### High Availability

1. **Distributed Monitoring**
   ```cfg
   define host {
       use                     linux-server
       host_name               satellite-server
       alias                   Satellite Server
       address                 192.168.1.20
       check_command          check-host-alive
   }
   
   define service {
       use                    generic-service
       host_name              satellite-server
       service_description    NSCA
       check_command         check_nsca
       check_interval        5
   }
   ```

2. **Failover Configuration**
   ```cfg
   # Primary server
   define host {
       use                     linux-server
       host_name               nagios-primary
       alias                   Primary Nagios Server
       address                 192.168.1.1
   }
   
   # Secondary server
   define host {
       use                     linux-server
       host_name               nagios-secondary
       alias                   Secondary Nagios Server
       address                 192.168.1.2
   }
   ```

## Best Practices

### Security Considerations

1. **Access Control**
   ```apache
   # Apache configuration for Nagios web interface
   <Directory "/usr/local/nagios/sbin">
       Options ExecCGI
       AllowOverride None
       Order allow,deny
       Allow from all
       AuthName "Nagios Access"
       AuthType Basic
       AuthUserFile /usr/local/nagios/etc/htpasswd.users
       Require valid-user
   </Directory>
   ```

2. **SSL Configuration**
   ```apache
   # Enable SSL
   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/nagios.crt
   SSLCertificateKeyFile /etc/ssl/private/nagios.key
   ```

### Monitoring Strategy

1. **Host Groups**
   ```cfg
   define hostgroup {
       hostgroup_name  web-servers
       alias           Web Servers
       members         web-server1, web-server2, web-server3
   }
   
   define hostgroup {
       hostgroup_name  db-servers
       alias           Database Servers
       members         db-server1, db-server2
   }
   ```

2. **Service Dependencies**
   ```cfg
   define servicedependency {
       host_name                       db-server
       service_description            MySQL
       dependent_host_name           web-server
       dependent_service_description Web Application
       execution_failure_criteria    w,u,c
       notification_failure_criteria w,u,c
   }
   ```

## Interview Tips
- Understand Nagios architecture
- Know monitoring capabilities
- Explain plugin system
- Understand configuration structure
- Be familiar with:
  - Check commands
  - Notification system
  - State management
  - Performance data
- Real-world scenarios:
  - Infrastructure monitoring
  - Service monitoring
  - Application monitoring
  - Network monitoring
- Best practices:
  - Configuration management
  - Security implementation
  - Performance tuning
  - High availability
- Advanced concepts:
  - Custom plugins
  - Distributed monitoring
  - Event handlers
  - Passive checks 