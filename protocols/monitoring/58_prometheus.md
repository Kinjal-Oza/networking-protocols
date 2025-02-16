# Prometheus

## Overview
Prometheus is an open-source systems monitoring and alerting toolkit, originally developed at SoundCloud. It features a multi-dimensional data model, a flexible query language (PromQL), autonomous server nodes, and a pull-based metrics collection approach.

## Technical Details

### Architecture Components

1. **Core Components**
   - Prometheus Server (Time Series Database)
   - Exporters (Metrics Collection)
   - Alertmanager (Alert Handling)
   - Pushgateway (Short-lived Jobs)
   - Service Discovery
   - Web UI

2. **Data Model**
   ```
   <metric_name>{<label_name>=<label_value>, ...} <value> [<timestamp>]
   
   # Example
   http_requests_total{status="200", method="GET"} 1234 1234567890
   ```

3. **Metric Types**
   - Counter (Cumulative values)
   - Gauge (Single numeric value)
   - Histogram (Distribution of values)
   - Summary (Similar to histogram with quantiles)

## Implementation

### Basic Configuration

1. **Prometheus Server (prometheus.yml)**
   ```yaml
   global:
     scrape_interval: 15s
     evaluation_interval: 15s
     external_labels:
       monitor: 'example-monitor'

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']

     - job_name: 'node-exporter'
       static_configs:
         - targets: ['localhost:9100']

   alerting:
     alertmanagers:
       - static_configs:
         - targets: ['localhost:9093']
   ```

2. **Alert Rules (alerts.yml)**
   ```yaml
   groups:
   - name: example-alert-rules
     rules:
     - alert: HighRequestLatency
       expr: http_request_duration_seconds > 1
       for: 10m
       labels:
         severity: warning
       annotations:
         summary: High request latency on {{ $labels.instance }}
         description: Request latency is above 1s (current value: {{ $value }}s)
   ```

### Metric Collection

1. **Custom Exporter Implementation**
   ```python
   from prometheus_client import start_http_server, Counter, Gauge
   import random
   import time

   # Create metrics
   REQUEST_COUNT = Counter(
       'app_request_count',
       'Application Request Count',
       ['method', 'endpoint']
   )

   REQUEST_LATENCY = Gauge(
       'app_request_latency_seconds',
       'Application Request Latency',
       ['endpoint']
   )

   def process_request(endpoint, method, latency):
       """Process a request and record metrics"""
       REQUEST_COUNT.labels(method=method, endpoint=endpoint).inc()
       REQUEST_LATENCY.labels(endpoint=endpoint).set(latency)

   if __name__ == '__main__':
       # Start up the server to expose metrics
       start_http_server(8000)
       
       # Generate some requests
       while True:
           process_request(
               random.choice(['/home', '/metrics']),
               random.choice(['GET', 'POST']),
               random.random()
           )
           time.sleep(1)
   ```

2. **Service Discovery Configuration**
   ```yaml
   scrape_configs:
     - job_name: 'ec2-nodes'
       ec2_sd_configs:
         - region: us-west-2
           access_key: <access_key>
           secret_key: <secret_key>
           port: 9100
       
       relabel_configs:
         - source_labels: [__meta_ec2_tag_Name]
           target_label: instance
         - source_labels: [__meta_ec2_availability_zone]
           target_label: zone
   ```

### PromQL Examples

1. **Basic Queries**
   ```promql
   # Rate of requests over 5 minutes
   rate(http_requests_total[5m])

   # 95th percentile latency
   histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

   # Aggregate by label
   sum by (status_code) (rate(http_requests_total[5m]))
   ```

2. **Advanced Queries**
   ```promql
   # Alert condition: Error rate > 5%
   sum(rate(http_requests_total{status=~"5.."}[5m])) 
     / 
   sum(rate(http_requests_total[5m])) * 100 > 5

   # CPU usage per instance
   100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

   # Memory usage
   node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes
   ```

## Performance Optimization

### Storage Optimization

1. **TSDB Configuration**
   ```yaml
   storage:
     tsdb:
       path: /data
       retention.time: 15d
       retention.size: 512GB
       wal-compression: true
       min-block-duration: 2h
       max-block-duration: 24h
   ```

2. **Query Optimization**
   ```yaml
   query:
     max-samples: 50000000
     timeout: 2m
     max-concurrency: 20
     lookback-delta: 5m
   ```

### High Availability

1. **Prometheus Federation**
   ```yaml
   scrape_configs:
     - job_name: 'federate'
       scrape_interval: 15s
       honor_labels: true
       metrics_path: '/federate'
       params:
         'match[]':
           - '{job="prometheus"}'
           - '{__name__=~"job:.*"}'
       static_configs:
         - targets:
           - 'prometheus-1:9090'
           - 'prometheus-2:9090'
   ```

2. **Thanos Integration**
   ```yaml
   thanos:
     image: thanosio/thanos:v0.24.0
     args:
       - "sidecar"
       - "--tsdb.path=/prometheus"
       - "--prometheus.url=http://localhost:9090"
       - "--objstore.config-file=/etc/thanos/bucket.yml"
   ```

## Best Practices

### Security Considerations

1. **TLS Configuration**
   ```yaml
   tls_config:
     cert_file: /etc/prometheus/cert.pem
     key_file: /etc/prometheus/key.pem
     client_auth_type: "RequireAndVerifyClientCert"
     client_ca_file: /etc/prometheus/ca.pem
   ```

2. **Authentication**
   ```yaml
   basic_auth:
     username: admin
     password_file: /etc/prometheus/password.txt
   ```

### Monitoring Strategy

1. **Alert Configuration**
   ```yaml
   alerting:
     alert_relabel_configs:
       - source_labels: [severity]
         regex: warning|critical
         action: keep
     alertmanagers:
       - scheme: https
         static_configs:
           - targets: ['alertmanager:9093']
   ```

2. **Recording Rules**
   ```yaml
   groups:
     - name: example-rules
       interval: 5m
       rules:
         - record: job:http_requests:rate5m
           expr: sum by (job) (rate(http_requests_total[5m]))
   ```

## Interview Tips
- Understand Prometheus architecture
- Know metric types and use cases
- Explain PromQL basics
- Understand service discovery
- Be familiar with:
  - Configuration options
  - Alert management
  - Storage options
  - High availability
- Real-world scenarios:
  - Container monitoring
  - Application metrics
  - Infrastructure monitoring
  - Alert management
- Best practices:
  - Query optimization
  - Storage configuration
  - Alert configuration
  - Federation setup
- Advanced concepts:
  - Remote storage
  - Federation
  - Service discovery
  - Custom exporters 