# Grafana Mac Monitoring - Setup Guide

This guide provides step-by-step instructions for accessing and configuring your Mac monitoring dashboard.

## ✅ Installation Complete

All monitoring components have been installed and started:

| Component | Status | Port | Purpose |
|-----------|--------|------|---------|
| **node_exporter** | ✅ Running | 9100 | Collects Mac system metrics |
| **Prometheus** | ✅ Running | 9090 | Stores time-series data |
| **Grafana** | ✅ Running | 3000 | Visualizes metrics in dashboards |

All services are configured to start automatically on system boot.

## 🚀 Quick Start

### Step 1: Access Grafana

1. Open your web browser and navigate to:
   ```
   http://localhost:3000
   ```

2. **Login with default credentials**:
   - Username: `admin`
   - Password: `admin`

3. You'll be prompted to change password on first login (recommended)

### Step 2: Add Prometheus Data Source

1. In Grafana, click **☰** menu → **Connections** → **Data Sources**
2. Click **Add data source**
3. Select **Prometheus**
4. Configure the connection:
   - **Name**: `Prometheus`
   - **URL**: `http://localhost:19090` (Note: Changed from 9090 due to port conflict with Antigravity agent)
   - **Access**: Server (default)
5. Scroll down and click **Save & Test**
6. You should see: ✅ "Data source is working"

### Step 3: Import Node Exporter Dashboard

1. Click **☰** menu → **Dashboards** → **Import**
2. Enter dashboard ID: **1860** (Node Exporter Full)
3. Click **Load**
4. Select **Prometheus** as the data source
5. Click **Import**

You now have a comprehensive Mac monitoring dashboard showing:
- CPU usage per core
- Memory utilization
- Disk I/O
- Network traffic
- Filesystem usage
- System load
- And much more!

## 📊 Accessing Your Monitoring

### Grafana Dashboard
- URL: http://localhost:3000
- Login: admin / (your password)

### Prometheus Web UI
- URL: http://localhost:19090
- Use to query raw metrics and view targets

### Node Exporter Metrics
- URL: http://localhost:9100/metrics
- Raw metrics endpoint

## 🔧 Managing Services

All services are managed via Homebrew:

```bash
# View service status
brew services list

# Restart a service
brew services restart node_exporter
brew services restart prometheus
brew services restart grafana

# Stop a service
brew services stop node_exporter
brew services stop prometheus
brew services stop grafana

# Start a service
brew services start node_exporter
brew services start prometheus
brew services start grafana
```

## 📝 Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| Prometheus config | `/opt/homebrew/etc/prometheus.yml` | Scrape configuration |
| Grafana config | `/opt/homebrew/etc/grafana/grafana.ini` | Grafana settings |
| Node exporter args | `/opt/homebrew/etc/node_exporter.args` | Runtime arguments |

## 🔍 Verification Commands

```bash
# Check if services are running
brew services list | grep -E "node_exporter|prometheus|grafana"

# Test node_exporter is collecting metrics
curl http://localhost:9100/metrics | head -20

# Check Prometheus targets
curl http://localhost:19090/api/v1/targets

# Verify Grafana responds
curl -I http://localhost:3000
```

## 📈 Key Metrics to Monitor

Once your dashboard is imported, you can monitor:

- **CPU**: Real-time CPU usage per core, system vs user time
- **Memory**: Available memory, usage percentage, swap activity
- **Disk**: Read/write rates, disk usage per filesystem
- **Network**: Incoming/outgoing traffic, errors, dropped packets
- **System Load**: 1min, 5min, 15min load averages
- **Processes**: Number of running processes, threads
- **Temperature**: CPU temperature (if available on Mac)

## 🎨 Customizing Dashboards

You can create your own dashboards:

1. Click **☰** → **Dashboards** → **New Dashboard**
2. Click **Add visualization**
3. Select **Prometheus** as data source
4. Build queries using metrics like:
   - `node_cpu_seconds_total`
   - `node_memory_MemAvailable_bytes`
   - `node_disk_io_time_seconds_total`
   - `node_network_receive_bytes_total`

## 🛠 Troubleshooting

### Services Won't Start
```bash
# Check logs
tail -f /opt/homebrew/var/log/prometheus.log
tail -f /opt/homebrew/var/log/grafana/grafana.log
```

### Prometheus Not Scraping node_exporter
1. Verify node_exporter is running: `curl http://localhost:9100/metrics`
2. Check Prometheus config: `/opt/homebrew/etc/prometheus.yml`
3. Restart Prometheus: `brew services restart prometheus`
4. Check targets in Prometheus UI: http://localhost:19090/targets

### Can't Connect to Grafana
1. Verify service is running: `brew services list | grep grafana`
2. Check port 3000 isn't blocked: `lsof -i :3000`
3. Try accessing: http://127.0.0.1:3000

## 🔐 Security Notes

> [!WARNING]
> The default setup is for **local monitoring only**. Grafana is not secured for external access.

To expose Grafana externally (not recommended without proper security):
1. Enable authentication
2. Use HTTPS
3. Configure firewall rules
4. Set strong passwords

## 📚 Next Steps

- **Set up alerts**: Configure Grafana alerts for critical metrics
- **Add more exporters**: Monitor databases, web servers, etc.
- **Create custom dashboards**: Tailor views to your needs
- **Explore metrics**: Learn PromQL for advanced queries

## 🆘 Need Help?

- Prometheus docs: https://prometheus.io/docs/
- Grafana docs: https://grafana.com/docs/
- Node Exporter: https://github.com/prometheus/node_exporter

---

Your Mac monitoring stack is now ready! 🎉
