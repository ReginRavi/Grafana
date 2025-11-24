# Architecture Overview

## High-Level System Diagram

```
┌─────────────────────────────────────────────────────┐
│                  USER BROWSER                       │
│            http://localhost:3000                    │
└──────────────────┬──────────────────────────────────┘
                   │
                   ├─── View Metrics & Dashboards
                   ↓
┌─────────────────────────────────────────────────────┐
│               GRAFANA :3000                         │
│          (Visualization Layer)                      │
└──────────────────┬──────────────────────────────────┘
                   │
                   ├─── Query Metrics (PromQL)
                   ↓
┌─────────────────────────────────────────────────────┐
│            PROMETHEUS :19090                        │
│         (Time-Series Database)                      │
└──────────────────┬──────────────────────────────────┘
                   │
                   ├─── Scrape Metrics (HTTP)
                   │      Every 15 seconds
                   ↓
┌─────────────────────────────────────────────────────┐
│           NODE_EXPORTER :9100                       │
│          (Metrics Collector)                        │
└──────────────────┬──────────────────────────────────┘
                   │
                   ├─── Collect System Stats
                   ↓
┌─────────────────────────────────────────────────────┐
│              macOS SYSTEM                           │
│    CPU | Memory | Disk | Network | Processes        │
└─────────────────────────────────────────────────────┘
```

## Data Flow

1. **macOS System** → Generates system metrics (CPU, memory, disk, network)
2. **node_exporter** (Port 9100) → Collects and exposes metrics at `/metrics` endpoint
3. **Prometheus** (Port 19090) → Scrapes metrics every 15 seconds, stores in time-series DB
4. **Grafana** (Port 3000) → Queries Prometheus and visualizes data in dashboards
5. **User** → Views dashboards via web browser

## Component Summary

| Component | Port | Role | Technology |
|-----------|------|------|------------|
| **macOS System** | - | Metric source | Native OS |
| **node_exporter** | 9100 | Metric collector | Prometheus exporter |
| **Prometheus** | 19090* | Time-series database | Prometheus TSDB |
| **Grafana** | 3000 | Visualization | Grafana dashboards |

*Note: Port 19090 is used instead of standard 9090 due to conflict with Antigravity agent.

## Network Connectivity

All services run locally on `localhost`:
- **node_exporter**: `http://localhost:9100/metrics`
- **Prometheus**: `http://localhost:19090`
- **Grafana**: `http://localhost:3000`

## Auto-Start Configuration

All three services are configured as macOS LaunchAgents and start automatically on boot:
- `~/Library/LaunchAgents/homebrew.mxcl.node_exporter.plist`
- `~/Library/LaunchAgents/homebrew.mxcl.prometheus.plist`
- `~/Library/LaunchAgents/homebrew.mxcl.grafana.plist`
