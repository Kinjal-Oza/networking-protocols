# Prometheus

## Overview
Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud. It features a multi-dimensional data model, a flexible query language (PromQL), efficient time series database, and a modern alerting approach.

## Technical Details

### Core Components
1. **Time Series Database**
   - Multi-dimensional data model
   - Custom query language (PromQL)
   - Pull-based metrics collection
   - Local storage
   - High availability options

2. **Data Model**
   - Metrics names
   - Key-value pairs (labels)
   - Timestamps
   - Float64 values
   - Four core metric types:
     - Counter
     - Gauge
     - Histogram
     - Summary

3. **Service Discovery**
   - File-based
   - DNS
   - Kubernetes
   - Cloud platforms (AWS, GCP, Azure)
   - Custom service discovery

### PromQL (Prometheus Query Language)

1. **Basic Queries**
   ```promql
   # Simple metric selection
   http_requests_total

   # With label matching
   http_requests_total{status="200", method="GET"}

   # Range vector
   http_requests_total{status="200"}[5m]

   # Rate calculation
   rate(http_requests_total[5m])
   ```

2. **Aggregation Operations**
   ```promql
   # Sum by status
   sum(http_requests_total) by (status)

   # Average by instance
   avg(node_cpu_seconds_total) by (instance)

   # 95th percentile
   histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
   ```

## Implementation

### Basic Configuration

1. **prometheus.yml**
   ```yaml
   global:
     scrape_interval: 15s
     evaluation_interval: 15s

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']

     - job_name: 'node'
       static_configs:
         - targets: ['localhost:9100']
   ```

2. **Alert Rules**
   ```yaml
   groups:
   - name: example
     rules:
     - alert: HighRequestLatency
       expr: http_request_duration_seconds > 0.5
       for: 10m
       labels:
         severity: warning
       annotations:
         summary: High request latency on {{ $labels.instance }}
   ```

### Advanced Implementation

1. **Custom Metrics (Python)**
   ```python
   from prometheus_client import Counter, Gauge, Histogram, start_http_server

   # Counter metric
   requests_total = Counter(
       'http_requests_total',
       'Total HTTP requests',
       ['method', 'endpoint']
   )

   # Gauge metric
   in_progress = Gauge(
       'http_requests_in_progress',
       'Number of in-progress HTTP requests'
   )

   # Histogram metric
   request_duration = Histogram(
       'http_request_duration_seconds',
       'HTTP request duration in seconds',
       ['endpoint']
   )

   # Start metrics server
   start_http_server(8000)
   ```

2. **High Availability Setup**
   ```yaml
   global:
     external_labels:
       cluster: 'cluster1'
       replica: 'replica1'

   rule_files:
     - 'rules/*.yml'

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']

   alerting:
     alertmanagers:
     - static_configs:
       - targets: ['alertmanager:9093']

   storage:
     tsdb:
       path: /prometheus
       retention.time: 15d
     remote_write:
       - url: "http://remote-write-endpoint/api/v1/write"
     remote_read:
       - url: "http://remote-read-endpoint/api/v1/read"
   ```

## Design Considerations

### Architecture Planning
1. **Scalability**
   - Federation
   - Remote storage
   - Sharding
   - Load balancing
   - High availability

2. **Data Retention**
   - Storage requirements
   - Retention period
   - Compaction
   - Remote storage options
   - Backup strategy

3. **Performance Optimization**
   - Query optimization
   - Recording rules
   - Label cardinality
   - Scrape intervals
   - Resource allocation

### Security Implementation
1. **Access Control**
   - TLS encryption
   - Authentication
   - Authorization
   - Network segmentation
   - Secure endpoints

2. **Data Protection**
   - Encryption at rest
   - Secure communication
   - Backup encryption
   - Audit logging
   - Access monitoring

## Best Practices

### Implementation Guidelines
1. **Metric Design**
   - Use clear naming conventions
   - Keep cardinality under control
   - Choose appropriate metric types
   - Document metrics
   - Use labels effectively

2. **Alerting Rules**
   - Define clear alert conditions
   - Avoid alert fatigue
   - Set appropriate thresholds
   - Include runbooks
   - Use alert severity levels

3. **Operational Procedures**
   - Regular backups
   - Monitoring the monitor
   - Capacity planning
   - Performance tuning
   - Incident response

## Interview Tips
- Understand Prometheus architecture
- Know metric types and use cases
- Explain PromQL basics
- Discuss scaling strategies
- Be familiar with:
  - Service discovery
  - Alert management
  - Storage options
  - Integration patterns
- Real-world scenarios:
  - Kubernetes monitoring
  - Microservices observability
  - Multi-datacenter setup
  - Cloud-native monitoring
- Best practices:
  - Metric naming
  - Alert design
  - Query optimization
  - High availability
- Advanced concepts:
  - Federation
  - Remote storage
  - Custom exporters
  - Recording rules 