# Port Conflict Resolution

## Issue
During the initial setup, Prometheus was configured to use the standard port `9090`. However, this port was already in use by the **Antigravity agent** (Electron process), preventing Prometheus from being accessible via its web UI and API.

## Symptoms
- `curl http://localhost:9090/api/v1/targets` returned "Not found"
- Browser navigation to `http://localhost:9090` showed Antigravity agent UI instead of Prometheus
- Grafana dashboard showed "No data" errors because it couldn't reach Prometheus

## Investigation
```bash
lsof -i :9090
```

Output showed both Electron (PID 1686) and Prometheus competing for the same port, with Electron taking precedence.

## Solution
Changed Prometheus to listen on port **19090** instead:

1. Updated `/opt/homebrew/etc/prometheus.args`:
   ```
   --config.file /opt/homebrew/etc/prometheus.yml
   --web.listen-address=127.0.0.1:19090
   --storage.tsdb.path /opt/homebrew/var/prometheus
   ```

2. Restarted Prometheus:
   ```bash
   brew services restart prometheus
   ```

3. Updated Grafana data source URL to `http://localhost:19090`

4. Verified the fix:
   ```bash
   curl 'http://localhost:19090/api/v1/query?query=up'
   ```
   
   Returned successful JSON response showing node_exporter is UP.

## Result
✅ Prometheus now accessible on port 19090  
✅ Grafana dashboard now displaying live Mac metrics  
✅ All documentation updated to reflect port 19090  

## Prevention
When setting up Prometheus on a system running the Antigravity agent or similar tools, avoid using ports in the 9090-9092 range. Use higher port numbers like 19090 or configure a different port range entirely.
