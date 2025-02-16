# Container Network Interface (CNI)

## Overview
Container Network Interface (CNI) is a specification and set of libraries for configuring network interfaces in Linux containers. It provides a common interface between container runtimes and network plugins, enabling standardized network connectivity for containerized applications.

## Technical Details

### Architecture Components

1. **Core Components**
   - CNI Plugin Interface
   - Runtime Configuration
   - Network Configuration
   - IPAM Plugin
   - Meta Plugins
   - Container Runtime Integration

2. **Plugin Types**
   - Main/Interface Plugins
     - bridge
     - macvlan
     - ipvlan
     - loopback
     - ptp
   - IPAM Plugins
     - host-local
     - dhcp
     - static
   - Meta Plugins
     - flannel
     - tuning
     - portmap
     - bandwidth

### CNI Specification

1. **Plugin Interface**
   ```go
   type CNI interface {
       Add(net *NetworkConfig, rt *RuntimeConf) (*types.Result, error)
       Del(net *NetworkConfig, rt *RuntimeConf) error
       Check(net *NetworkConfig, rt *RuntimeConf) error
       Version() string
   }
   ```

2. **Network Configuration**
   ```json
   {
     "cniVersion": "0.4.0",
     "name": "mynet",
     "type": "bridge",
     "bridge": "cni0",
     "isGateway": true,
     "ipMasq": true,
     "ipam": {
       "type": "host-local",
       "subnet": "10.22.0.0/16",
       "routes": [
         { "dst": "0.0.0.0/0" }
       ]
     }
   }
   ```

## Implementation

### Basic Configuration

1. **Bridge Plugin Setup**
   ```bash
   # Network configuration file
   cat > /etc/cni/net.d/10-mynet.conf <<EOF
   {
     "cniVersion": "0.4.0",
     "name": "mynet",
     "type": "bridge",
     "bridge": "cni0",
     "isGateway": true,
     "ipMasq": true,
     "ipam": {
       "type": "host-local",
       "subnet": "10.22.0.0/16"
     }
   }
   EOF
   ```

2. **Plugin Installation**
   ```bash
   # Install CNI plugins
   mkdir -p /opt/cni/bin
   curl -L https://github.com/containernetworking/plugins/releases/download/v0.9.1/cni-plugins-linux-amd64-v0.9.1.tgz | tar -xz -C /opt/cni/bin
   ```

### Advanced Configuration

1. **Chained Plugins**
   ```json
   {
     "cniVersion": "0.4.0",
     "name": "mynet",
     "plugins": [
       {
         "type": "bridge",
         "bridge": "cni0",
         "ipam": {
           "type": "host-local",
           "subnet": "10.22.0.0/16"
         }
       },
       {
         "type": "portmap",
         "capabilities": {"portMappings": true}
       },
       {
         "type": "bandwidth",
         "ingressRate": 123456,
         "ingressBurst": 456789
       }
     ]
   }
   ```

2. **Custom IPAM Configuration**
   ```json
   {
     "cniVersion": "0.4.0",
     "name": "mynet",
     "type": "bridge",
     "ipam": {
       "type": "host-local",
       "ranges": [
         [
           {
             "subnet": "10.22.0.0/16",
             "rangeStart": "10.22.0.50",
             "rangeEnd": "10.22.0.200"
           }
         ]
       ],
       "routes": [
         { "dst": "0.0.0.0/0" }
       ]
     }
   }
   ```

## Design Considerations

### Architecture Planning
1. **Network Design**
   - Plugin selection
   - IPAM strategy
   - Network topology
   - Security requirements
   - Performance needs

2. **Integration Strategy**
   - Container runtime
   - Orchestration platform
   - Existing infrastructure
   - Monitoring system
   - Security tools

3. **Performance Impact**
   - Plugin overhead
   - IPAM latency
   - Network throughput
   - Resource utilization
   - Scaling limits

### High Availability
1. **Redundancy Planning**
   - Multiple networks
   - Failover configuration
   - Load distribution
   - State management
   - Recovery procedures

2. **Monitoring Strategy**
   - Network health
   - Performance metrics
   - Error tracking
   - Resource usage
   - Plugin status

## Troubleshooting

### Common Issues
1. **Network Problems**
   - IP allocation failures
   - DNS resolution issues
   - Network connectivity
   - Plugin conflicts
   - Configuration errors

2. **Performance Issues**
   - Slow network operations
   - Resource exhaustion
   - Plugin timeouts
   - IPAM bottlenecks
   - MTU mismatches

3. **Integration Issues**
   - Runtime compatibility
   - Plugin version conflicts
   - Configuration syntax
   - Permission problems
   - Path resolution

### Debugging Tools
```bash
# Check CNI configuration
ls /etc/cni/net.d/

# Verify plugin installation
ls /opt/cni/bin/

# Test plugin directly
CNI_COMMAND=ADD CNI_CONTAINERID=test CNI_NETNS=/var/run/netns/test CNI_IFNAME=eth0 ./bridge < config.json

# View CNI logs
journalctl -u kubelet | grep cni

# Check network namespace
ip netns list
ip netns exec <namespace> ip addr
```

## Best Practices

### Implementation Guidelines
1. **Network Configuration**
   - Use semantic versioning
   - Document configurations
   - Implement security
   - Monitor performance
   - Plan for scale

2. **Plugin Management**
   - Regular updates
   - Version control
   - Testing strategy
   - Backup procedures
   - Rollback plans

### Security Considerations
1. **Network Security**
   ```json
   {
     "cniVersion": "0.4.0",
     "name": "secured-network",
     "type": "bridge",
     "bridge": "cni0",
     "ipam": {
       "type": "host-local",
       "subnet": "10.22.0.0/16"
     },
     "policy": {
       "type": "k8s",
       "k8s_api_root": "https://api.k8s.cluster",
       "k8s_auth_token": "eyJ0eXAi..."
     }
   }
   ```

2. **Access Control**
   ```bash
   # Set proper permissions
   chmod 600 /etc/cni/net.d/*
   chown root:root /etc/cni/net.d/*
   
   # Secure plugin directory
   chmod 755 /opt/cni/bin
   chown root:root /opt/cni/bin
   ```

## Interview Tips
- Understand CNI specification
- Know plugin types and uses
- Explain network configuration
- Understand IPAM concepts
- Be familiar with:
  - Plugin architecture
  - Configuration options
  - Debugging methods
  - Security features
- Real-world scenarios:
  - Kubernetes integration
  - Multi-network setup
  - Custom plugin development
  - Network policy
- Best practices:
  - Plugin selection
  - Configuration management
  - Security implementation
  - Performance tuning
- Advanced concepts:
  - Meta plugins
  - Chained plugins
  - Custom IPAM
  - Network policy 