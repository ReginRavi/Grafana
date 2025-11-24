# macOS Memory Metrics Guide

## Available Memory Metrics on macOS

macOS node_exporter provides different memory metrics than Linux:

### Total Memory
```promql
node_memory_total_bytes{instance="$node", job="$job"}
```
Example: 17,179,869,184 bytes (17.2 GB)

### Used Memory Calculation
macOS memory usage = wired + active + compressed

```promql
(
  node_memory_wired_bytes{instance="$node", job="$job"} + 
  node_memory_active_bytes{instance="$node", job="$job"} + 
  node_memory_compressed_bytes{instance="$node", job="$job"}
)
```

### RAM Used Percentage
```promql
(
  (
    node_memory_wired_bytes{instance="$node", job="$job"} + 
    node_memory_active_bytes{instance="$node", job="$job"} + 
    node_memory_compressed_bytes{instance="$node", job="$job"}
  ) / node_memory_total_bytes{instance="$node", job="$job"}
) * 100
```

### Available Memory
Free + Inactive memory (can be reclaimed)

```promql
node_memory_free_bytes{instance="$node", job="$job"} + 
node_memory_inactive_bytes{instance="$node", job="$job"}
```

### Available Memory Percentage
```promql
(
  (
    node_memory_free_bytes{instance="$node", job="$job"} + 
    node_memory_inactive_bytes{instance="$node", job="$job"}
  ) / node_memory_total_bytes{instance="$node", job="$job"}
) * 100
```

## Metric Breakdown

| Metric | Description | Example Value |
|--------|-------------|---------------|
| `node_memory_total_bytes` | Total physical RAM | 17.2 GB |
| `node_memory_wired_bytes` | Memory locked in RAM (can't be paged out) | 1.86 GB |
| `node_memory_active_bytes` | Recently used memory | 3.79 GB |
| `node_memory_inactive_bytes` | Not recently used, can be reclaimed | 3.75 GB |
| `node_memory_compressed_bytes` | Compressed memory | 7.08 GB |
| `node_memory_free_bytes` | Completely free memory | 120 MB |
| `node_memory_purgeable_bytes` | Can be purged if needed | 49 MB |

## Dashboard Query Examples

### Gauge Panel: RAM Used
**Query**:
```promql
(node_memory_wired_bytes + node_memory_active_bytes + node_memory_compressed_bytes) / 1024 / 1024 / 1024
```
**Unit**: GiB (gigabytes)

### Gauge Panel: RAM Total
**Query**:
```promql
node_memory_total_bytes / 1024 / 1024 / 1024
```
**Unit**: GiB (gigabytes)

### Stat Panel: RAM Usage %
**Query**:
```promql
((node_memory_wired_bytes + node_memory_active_bytes + node_memory_compressed_bytes) / node_memory_total_bytes) * 100
```
**Unit**: percent (0-100)

### Graph Panel: Memory Breakdown Over Time
**Multiple queries**:
1. Wired: `node_memory_wired_bytes`
2. Active: `node_memory_active_bytes`
3. Compressed: `node_memory_compressed_bytes`
4. Inactive: `node_memory_inactive_bytes`
5. Free: `node_memory_free_bytes`

## Why Linux Queries Don't Work

The imported dashboard (Node Exporter Full, ID 1860) uses Linux-specific metrics:
- `node_memory_MemAvailable_bytes` (Linux only)
- `node_memory_MemTotal_bytes` (Linux only)

These metrics don't exist on macOS, causing "No data" errors in RAM panels.

## Solution Options

### Option 1: Edit Existing Dashboard Panels
Manually edit each RAM panel in Grafana to use macOS-compatible queries.

### Option 2: Create Custom macOS Dashboard
Create a new dashboard specifically designed for macOS metrics.

### Option 3: Use macOS-Specific Dashboard
Search for a dashboard designed for macOS monitoring (less common).

## Quick Test

Test if macOS memory metrics are being collected:

```bash
curl -s http://localhost:9100/metrics | grep node_memory_total_bytes
```

Should return: `node_memory_total_bytes 1.7179869184e+10`

Test in Prometheus:
```bash
curl -s 'http://localhost:19090/api/v1/query?query=node_memory_total_bytes' | jq
```

Test in Grafana Explore view:
1. Navigate to Explore
2. Select Prometheus data source
3. Enter query: `node_memory_total_bytes`
4. Run query
