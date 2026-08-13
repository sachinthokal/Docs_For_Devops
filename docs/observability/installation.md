# Observability Stack Installation Guide ⚙️

Step-by-step installation guide for setting up **Prometheus, Grafana, Node Exporter, and Loki** using Docker Compose.

---

## 1. Complete Observability Stack (`docker-compose.yml`)

Create a `docker-compose.yml` file:

```yaml
version: '3.8'

networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data:
  grafana_data:

services:
  prometheus:
    image: prom/prometheus:v2.45.0
    container_name: prometheus
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    networks:
      - monitoring

  node-exporter:
    image: prom/node-exporter:v1.6.0
    container_name: node-exporter
    restart: unless-stopped
    ports:
      - "9100:9100"
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:10.0.0
    container_name: grafana
    restart: unless-stopped
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - monitoring
```

---

## 2. Prometheus Scrape Configuration (`prometheus.yml`)

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

---

## 3. Starting the Stack

```bash
# Launch observability containers
docker compose up -d

# Verify container status
docker compose ps
```
- **Prometheus Dashboard**: `http://localhost:9090`
- **Grafana UI**: `http://localhost:3000` *(Default login: admin / admin)*
- **Node Exporter Metrics**: `http://localhost:9100/metrics`