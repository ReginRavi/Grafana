# Quick Fix: RAM Panel Queries for macOS

## Problem
The imported "Node Exporter Full" dashboard (ID 1860) is designed for Linux and uses memory metrics that don't exist on macOS:
- ❌ `node_memory_MemAvailable_bytes` (Linux only)
- ❌ `node_memory_MemTotal_bytes` (Linux only)

## Solution: Use macOS-Compatible Queries

### ✅ RAM Used (in GiB)
```promql
(node_memory_wired_bytes{instance="$node", job="$job"} + node_memory_active_bytes{instance="$node", job="$job"} + node_memory_compressed_bytes{instance="$node", job="$job"}) / 1024 / 1024 / 1024
```
**Result**: Shows used RAM in gigabytes (e.g., 14 GiB)

### ✅ RAM Total (in GiB)
```promql
node_memory_total_bytes{instance="$node", job="$job"} / 1024 / 1024 / 1024
```
**Result**: Shows total RAM (e.g., 16 GiB or 17.2 GiB)

### ✅ RAM Usage Percentage
```promql
((node_memory_wired_bytes{instance="$node", job="$job"} + node_memory_active_bytes{instance="$node", job="$job"} + node_memory_compressed_bytes{instance="$node", job="$job"}) / node_memory_total_bytes{instance="$node", job="$job"}) * 100
```
**Result**: Shows percentage (e.g., 81.5%)

### ✅ RAM Available (in GiB)
```promql
(node_memory_free_bytes{instance="$node", job="$job"} + node_memory_inactive_bytes{instance="$node", job="$job"}) / 1024 / 1024 / 1024
```
**Result**: Shows available RAM (free + inactive that can be reclaimed)

## How to Apply These Fixes

1. **Navigate to Dashboard**: http://localhost:3000/d/rYdddlPWk/node-exporter-full

2. **Edit Panel**:
   - Hover over the panel title (e.g., "RAM Total", "SWAP Used", "Memory Basic")
   - Click the **dropdown arrow** → **Edit**
   - Or click **Edit** button if panel is already selected

3. **Replace Query**:
   - In the query editor, delete the existing query
   - Paste the appropriate macOS query from above
   - Click **Run queries** button to preview

4. **Save**:
   - Click **Apply** (top right, or back arrow)
   - Click **Save dashboard** (disk icon in header)
   - Add description like "Fixed for macOS"
   - Click **Save**

## Panels to Fix

Based on the Node Exporter Full dashboard, you'll likely need to update these panels:

- [ ] **RAM Used** - ✅ **FIXED** (now shows 14 GiB)
- [ ] **RAM Total** - Use the "RAM Total" query above
- [ ] **RAM Usage %** - Use the "RAM Usage Percentage" query
- [ ] **SWAP Used** - macOS typically doesn't use swap, query: `node_memory_swap_used_bytes / 1024 / 1024 / 1024`
- [ ] **SWAP Total** - Query: `node_memory_swap_total_bytes / 1024 / 1024 / 1024`
- [ ] **Memory Basic** (graph) - Update to use macOS memory metrics

## Verification

Test queries directly in Prometheus:

```bash
# Test RAM total
curl -s 'http://localhost:19090/api/v1/query?query=node_memory_total_bytes/1024/1024/1024'

# Test RAM used  
curl -s 'http://localhost:19090/api/v1/query?query=(node_memory_wired_bytes%2Bnode_memory_active_bytes%2Bnode_memory_compressed_bytes)/1024/1024/1024'
```

Or use Grafana Explore:
1. Go to http://localhost:3000/explore
2. Select "prometheus" data source
3. Paste query and click "Run query"

## Why This Works

macOS memory model:
- **Used Memory** = Wired + Active + Compressed
- **Available** = Free + Inactive (can be reclaimed by the system)
- **Total** = All physical RAM installed

This differs from Linux which uses `MemAvailable` (kernel calculation of free memory).

## Need More Help?

See the detailed guide: [`MACOS_MEMORY_METRICS.md`](file:///Users/reginravi/Documents/grafana/MACOS_MEMORY_METRICS.md)
