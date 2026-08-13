# Observability Architecture & Telemetry Pipelines 🏛️

Detailed breakdown of modern observability architecture, OpenTelemetry standards, metric scrapers, and distributed tracing engines.

---

## 1. High-Level Telemetry Architecture

```
+-----------------------------------------------------------------------------------+
|                                APPLICATION LAYER                                  |
|                                                                                   |
|  +---------------------+    +---------------------+    +-----------------------+  |
|  | Microservice A (Go) |    | Microservice B (Node)|    | Microservice C (Java) |  |
|  | - OTel SDK Auto-Inst|    | - OTel SDK Auto-Inst|    | - OTel SDK Auto-Inst|  |
|  +----------+----------+    +----------+----------+    +-----------+-----------+  |
+-------------|--------------------------|---------------------------|--------------+
              | Traces, Metrics, Logs    |                           |
              +--------------------------+---------------------------+
                                         | OTLP (gRPC / HTTP)
                                         v
+-----------------------------------------------------------------------------------+
|                             OPENTELEMETRY COLLECTOR                               |
|                                                                                   |
|  +---------------------+    +---------------------+    +-----------------------+  |
|  | Receivers (OTLP,    | -> | Processors (Batch,  | -> | Exporters (Prometheus,|  |
|  | Jaeger, Prometheus) |    | Memory, Attributes) |    | Loki, Jaeger, Tempo)  |  |
|  +---------------------+    +---------------------+    +-----------------------+  |
+----------------------------------------|------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|                             STORAGE & VISUALIZATION LAYER                         |
|                                                                                   |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  | Prometheus (Metrics|    | Loki / Elasticsearch|    | Jaeger / Tempo          |  |
|  | Time-Series DB)    |    | (Log Indexing)     |    | (Distributed Traces)    |  |
|  +---------+----------+    +---------+----------+    +------------+------------+  |
|            |                         |                            |               |
|            +-------------------------+----------------------------+               |
|                                      |                                            |
|                                      v                                            |
|                        +----------------------------+                             |
|                        |     GRAFANA DASHBOARDS     |                             |
|                        +----------------------------+                             |
+-----------------------------------------------------------------------------------+
```

---

## 2. Core Architectural Components

### A. OpenTelemetry (OTel) Standard
OpenTelemetry is a vendor-neutral Cloud Native Computing Foundation (CNCF) observability framework that provides standardized APIs, SDKs, and tooling to generate and export telemetry data.

### B. Prometheus Metric Engine
- **Pull Model**: Prometheus actively scrapes metrics over HTTP (`/metrics` endpoint) at configured time intervals.
- **Time Series DB (TSDB)**: Stores metrics as timestamped key-value pairs (`metric_name{label="value"} value timestamp`).

### C. Distributed Tracing (Jaeger / Grafana Tempo)
Tracks request flow across boundaries via **W3C Trace Context headers** (`traceparent`). Constructs a DAG showing latency per hop (Spans).