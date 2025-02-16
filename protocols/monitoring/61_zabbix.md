# Zabbix

## Overview
Zabbix is an enterprise-class open-source monitoring solution designed to monitor and track the status of various network services, servers, and network hardware. It provides advanced monitoring, alerting, and visualization features with a focus on scalability and flexibility.

## Technical Details

### Architecture Components

1. **Core Components**
   - Zabbix Server
   - Zabbix Database
   - Zabbix Web Interface
   - Zabbix Proxy
   - Zabbix Agent
   - Zabbix Java Gateway
   - API Interface

2. **Monitoring Capabilities**
   - Server Performance
   - Network Services
   - Applications
   - Databases
   - Cloud Resources
   - Virtual Machines
   - Web Scenarios
   - Custom Metrics

3. **Data Collection Methods**
   - Agent-based Monitoring
   - SNMP Monitoring
   - IPMI Monitoring
   - JMX Monitoring
   - HTTP Monitoring
   - SSH Monitoring
   - Telnet Monitoring
   - Custom Scripts

## Implementation

### Basic Configuration

1. **Server Configuration (zabbix_server.conf)**
   ```ini
   # Database Configuration
   DBHost=localhost
   DBName=zabbix
   DBUser=zabbix
   DBPassword=zabbix_pwd
   
   # Cache Settings
   CacheSize=8M
   HistoryCacheSize=16M
   TrendCacheSize=4M
   
   # Housekeeping
   HousekeepingFrequency=1
   MaxHousekeeperDelete=5000
   ```

2. **Agent Configuration (zabbix_agentd.conf)**
   ```ini
   # Basic Settings
   Server=192.168.1.1
   ServerActive=192.168.1.1
   Hostname=web-server1
   
   # Monitoring Items
   UserParameter=custom.metric[*],/usr/local/bin/check_metric.sh $1
   
   # TLS Settings
   TLSConnect=psk
   TLSPSKIdentity=PSK 001
   TLSPSKFile=/etc/zabbix/zabbix_agentd.psk
   ```

### Advanced Configuration

1. **Template Configuration**
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <zabbix_export>
       <version>5.0</version>
       <templates>
           <template>
               <template>Custom Application Template</template>
               <name>Custom Application Monitoring</name>
               <groups>
                   <group>
                       <name>Templates/Applications</name>
                   </group>
               </groups>
               <items>
                   <item>
                       <name>Application Status</name>
                       <type>0</type>
                       <key>custom.app.status</key>
                       <delay>30s</delay>
                       <history>90d</history>
                       <trends>365d</trends>
                       <value_type>3</value_type>
                   </item>
               </items>
               <triggers>
                   <trigger>
                       <expression>{Custom Application Template:custom.app.status.last()}=0</expression>
                       <name>Application is down</name>
                       <priority>4</priority>
                   </trigger>
               </triggers>
           </template>
       </templates>
   </zabbix_export>
   ```

2. **API Integration**
   ```python
   import requests
   import json

   class ZabbixAPI:
       def __init__(self, url, user, password):
           self.url = url + '/api_jsonrpc.php'
           self.auth = None
           self.authenticate(user, password)
           
       def authenticate(self, user, password):
           data = {
               'jsonrpc': '2.0',
               'method': 'user.login',
               'params': {
                   'user': user,
                   'password': password
               },
               'id': 1
           }
           response = requests.post(self.url, json=data)
           self.auth = response.json()['result']
           
       def get_hosts(self):
           data = {
               'jsonrpc': '2.0',
               'method': 'host.get',
               'params': {
                   'output': ['hostid', 'host']
               },
               'auth': self.auth,
               'id': 2
           }
           response = requests.post(self.url, json=data)
           return response.json()['result']
   ```

### Custom Monitoring

1. **Custom Script**
   ```bash
   #!/bin/bash
   # check_custom_metric.sh
   
   METRIC_NAME=$1
   THRESHOLD_WARNING=$2
   THRESHOLD_CRITICAL=$3
   
   # Get metric value
   VALUE=$(get_metric_value)
   
   if [ $VALUE -gt $THRESHOLD_CRITICAL ]; then
       echo "CRITICAL - $METRIC_NAME value is $VALUE"
       exit 2
   elif [ $VALUE -gt $THRESHOLD_WARNING ]; then
       echo "WARNING - $METRIC_NAME value is $VALUE"
       exit 1
   else
       echo "OK - $METRIC_NAME value is $VALUE"
       exit 0
   fi
   ```

2. **Low-Level Discovery**
   ```json
   {
       "data": [
           {
               "{#FSNAME}": "/",
               "{#FSTYPE}": "ext4"
           },
           {
               "{#FSNAME}": "/home",
               "{#FSTYPE}": "ext4"
           }
       ]
   }
   ```

## Performance Optimization

### Database Optimization

1. **PostgreSQL Configuration**
   ```ini
   # postgresql.conf
   max_connections = 500
   shared_buffers = 4GB
   effective_cache_size = 12GB
   maintenance_work_mem = 1GB
   checkpoint_completion_target = 0.9
   wal_buffers = 16MB
   default_statistics_target = 100
   random_page_cost = 1.1
   effective_io_concurrency = 200
   work_mem = 8MB
   min_wal_size = 1GB
   max_wal_size = 4GB
   ```

2. **Partitioning**
   ```sql
   -- Create partitioned history table
   CREATE TABLE history_uint (
       itemid bigint NOT NULL,
       clock integer NOT NULL,
       value numeric(20,0) NOT NULL,
       ns integer NOT NULL
   ) PARTITION BY RANGE (clock);
   
   -- Create partitions
   CREATE TABLE history_uint_y2023m01 PARTITION OF history_uint
       FOR VALUES FROM ('1672531200') TO ('1675209600');
   ```

### High Availability

1. **Proxy Configuration**
   ```ini
   # zabbix_proxy.conf
   Server=192.168.1.1
   Hostname=proxy-server1
   DBHost=localhost
   DBName=zabbix_proxy
   ConfigFrequency=100
   DataSenderFrequency=1
   StartPollers=100
   StartPollersUnreachable=50
   ```

2. **Load Balancing**
   ```nginx
   # nginx.conf
   upstream zabbix {
       server zabbix1.example.com:80;
       server zabbix2.example.com:80;
       server zabbix3.example.com:80;
   }
   
   server {
       listen 80;
       server_name zabbix.example.com;
       
       location / {
           proxy_pass http://zabbix;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
       }
   }
   ```

## Best Practices

### Security Considerations

1. **Encryption Configuration**
   ```ini
   # zabbix_server.conf
   TLSCAFile=/etc/zabbix/zabbix_ca_file
   TLSCRLFile=/etc/zabbix/zabbix_crl_file
   TLSCertFile=/etc/zabbix/zabbix_server.crt
   TLSKeyFile=/etc/zabbix/zabbix_server.key
   ```

2. **User Management**
   ```sql
   -- Create user role
   INSERT INTO role (roleid, name, type)
    VALUES (10, 'Limited Monitor', 1);
   
   -- Assign permissions
   INSERT INTO role_rule (roleid, type, name, value_int)
    VALUES (10, 0, 'ui.monitoring.hosts', 1);
   ```

### Monitoring Strategy

1. **Host Groups**
   ```xml
   <groups>
       <group>
           <name>Production Servers</name>
           <hosts>
               <host>web-server1</host>
               <host>web-server2</host>
               <host>db-server1</host>
           </hosts>
       </group>
   </groups>
   ```

2. **Maintenance Periods**
   ```json
   {
       "jsonrpc": "2.0",
       "method": "maintenance.create",
       "params": {
           "name": "Sunday maintenance",
           "active_since": 1640995200,
           "active_till": 1672531199,
           "timeperiods": [
               {
                   "timeperiod_type": 2,
                   "every": 1,
                   "dayofweek": 64,
                   "start_time": 7200,
                   "period": 3600
               }
           ],
           "hostids": ["10084", "10085"]
       },
       "auth": "038e1d7b1735c6a5436ee9eae095879e",
       "id": 1
   }
   ```

## Interview Tips
- Understand Zabbix architecture
- Know monitoring capabilities
- Explain data collection methods
- Understand template system
- Be familiar with:
  - Configuration options
  - API functionality
  - Performance tuning
  - Security features
- Real-world scenarios:
  - Enterprise monitoring
  - Distributed monitoring
  - Cloud monitoring
  - Application monitoring
- Best practices:
  - Template design
  - Discovery rules
  - Maintenance planning
  - Backup strategy
- Advanced concepts:
  - Custom scripts
  - Low-level discovery
  - Distributed monitoring
  - High availability 