# Observability Query Languages Cheat Sheet ⚡

PromQL (Prometheus Query Language) and LogQL (Grafana Loki) reference for querying telemetry data.

---

## 1. PromQL (Prometheus Query Language) Reference

### Metric Types
- **Counter**: Monotonically increasing cumulative metric (e.g., total requests).
- **Gauge**: Metric that can go up and down (e.g., CPU %, Memory usage).
- **Histogram / Summary**: Measures duration and size distribution.

### Essential PromQL Queries

```promql
# 1. Total CPU Usage Percentage across all instances
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# 2. Free Memory in Gigabytes
(node_memory_MemAvailable_bytes / 1024 / 1024 / 1024)

# 3. HTTP Request Rate (RPS) over 5 minute window
rate(http_requests_total[5m])

# 4. HTTP Error Rate (5xx status codes)
sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) * 100

# 5. 95th Percentile Latency (P95)
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```

---

## 2. LogQL (Grafana Loki) Reference

```logql
# Stream logs for a specific app container
{app="backend-api"}

# Filter logs containing specific string
{app="backend-api"} |= "error"

# Exclude logs containing string
{app="backend-api"} != "debug"

# Parse JSON logs and filter by status code >= 500
{app="backend-api"} | json | status >= 500

# Count error logs rate per minute
rate({app="backend-api"} |= "error" [1m])
```