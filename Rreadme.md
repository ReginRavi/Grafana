# Mac Monitoring with Grafana

A complete monitoring solution for macOS using Grafana, Prometheus, and node_exporter. Monitor your Mac's CPU, memory, disk, network, and system metrics in beautiful real-time dashboards.

## 🌟 Features

- **Real-time System Monitoring**: Track CPU usage, memory, disk I/O, network traffic
- **Beautiful Dashboards**: Pre-built Grafana dashboards with customizable visualizations
- **Historical Data**: Prometheus stores time-series data for trend analysis
- **Automatic Startup**: All services configured to start on boot
- **Easy Management**: Simple Homebrew-based installation and management

## 🏗 Architecture

```
┌─────────────┐
│   Grafana   │  ← Visualization (Port 3000)
│  Dashboard  │
└──────┬──────┘
       │
       │ Queries
       ▼
┌─────────────┐
│ Prometheus  │  ← Time-series DB (Port 9090)
└──────┬──────┘
       │
       │ Scrapes
       ▼
┌─────────────┐
│node_exporter│  ← Metrics Collector (Port 9100)
└─────────────┘
       │
       ▼
   Mac System
```

## 📦 Components

| Component | Version | Purpose |
|-----------|---------|---------|
| **Grafana** | Latest | Dashboard and visualization platform |
| **Prometheus** | Latest | Time-series database for metrics storage |
| **node_exporter** | Latest | System metrics collector for macOS |

## 🚀 Quick Start

All components are already installed and running!

### Access Your Dashboard

1. **Open Grafana**: http://localhost:3000
   - Login: `admin` / `admin` (change on first login)

2. **Add Prometheus data source** (first time only):
   - Go to **Connections** → **Data Sources** → **Add data source**
   - Select **Prometheus**
   - URL: `http://localhost:19090`
   - Click **Save & Test**

3. **Import dashboard**:
   - Go to **Dashboards** → **Import**
   - Enter ID: **1860** (Node Exporter Full)
   - Select **Prometheus** as data source
   - Click **Import**

That's it! Your Mac metrics are now being displayed in real-time. 📊

## 📚 Documentation

- **[Setup Guide](setup_guide.md)**: Complete installation and configuration instructions
- **[Prometheus Config](prometheus.yml)**: Metrics collection configuration

## 🔧 Service Management

```bash
# View all monitoring services
brew services list

# Restart services
brew services restart node_exporter
brew services restart prometheus
brew services restart grafana

# Stop services
brew services stop node_exporter prometheus grafana
```

## 🌐 Service URLs

- **Grafana Dashboard**: http://localhost:3000
- **Prometheus UI**: http://localhost:19090
- **Node Exporter Metrics**: http://localhost:9100/metrics

## 📊 What You Can Monitor

- ✅ CPU usage (per core and total)
- ✅ Memory utilization and swap
- ✅ Disk I/O and usage
- ✅ Network traffic and errors
- ✅ System load averages
- ✅ Filesystem usage
- ✅ Process and thread counts
- ✅ Temperature sensors

## 🛠 Troubleshooting

If services aren't working:

```bash
# Check service status
brew services list | grep -E "node_exporter|prometheus|grafana"

# Verify metrics collection
curl http://localhost:9100/metrics

# Check Prometheus targets
open http://localhost:9090/targets
```

See [setup_guide.md](setup_guide.md) for detailed troubleshooting.

## 📈 Next Steps

- Configure alerts for critical metrics
- Create custom dashboards
- Add more exporters (MySQL, PostgreSQL, etc.)
- Set up remote monitoring

## 🆘 Support

- **Grafana Documentation**: https://grafana.com/docs/
- **Prometheus Documentation**: https://prometheus.io/docs/
- **Node Exporter**: https://github.com/prometheus/node_exporter

---

**Monitoring Stack Status**: ✅ All services running

Last updated: 2025-11-24
