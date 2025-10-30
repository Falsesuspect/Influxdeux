# Quick Installation Guide

## Prerequisites

Before importing the dashboard, ensure you have:

1. ✅ **FFXIV with Dalamud**: Running the game with Dalamud plugin framework
2. ✅ **InfluxReborn Plugin**: Installed and configured in Dalamud
3. ✅ **AllaganTools Plugin**: (Optional) For inventory tracking
4. ✅ **SubmarineTracker Plugin**: (Optional) For submarine data
5. ✅ **InfluxDB or QuestDB**: Time-series database server running
6. ✅ **Grafana**: Visualization platform (version 9.0+)

## Step-by-Step Setup

### 1. Configure InfluxReborn Plugin

In FFXIV, configure the InfluxReborn plugin:

```
/influx
```

1. Select your database type (InfluxDB or QuestDB)
2. Enter server URL (e.g., `http://localhost:8086`)
3. Enter authentication token (for InfluxDB) or credentials
4. Set organization and bucket name
5. Test connection
6. Enable auto-enrollment for characters (recommended)
7. Configure which characters to track

### 2. Set Up InfluxDB

#### Using Docker (Recommended)

```bash
# InfluxDB 2.x
docker run -d \
  --name influxdb \
  -p 8086:8086 \
  -v influxdb-data:/var/lib/influxdb2 \
  influxdb:latest

# Access UI at http://localhost:8086
# Create initial user, org, and bucket
# Generate an API token
```

#### Using QuestDB

```bash
# QuestDB
docker run -d \
  --name questdb \
  -p 9000:9000 \
  -p 9009:9009 \
  -v questdb-data:/root/.questdb \
  questdb/questdb:latest

# Access UI at http://localhost:9000
```

### 3. Set Up Grafana

#### Using Docker

```bash
docker run -d \
  --name grafana \
  -p 3000:3000 \
  -v grafana-data:/var/lib/grafana \
  grafana/grafana:latest

# Access UI at http://localhost:3000
# Default credentials: admin/admin
```

#### Configure Data Source

1. Open Grafana at `http://localhost:3000`
2. Login (default: admin/admin)
3. Go to **Configuration** → **Data Sources** → **Add data source**
4. Select **InfluxDB** (or **QuestDB** if using QuestDB)
5. Configure:
   - **Query Language**: InfluxQL (for InfluxDB 1.x) or Flux (for InfluxDB 2.x)
   - **URL**: `http://localhost:8086` (adjust if different)
   - **Organization**: Your org name
   - **Token**: Your API token
   - **Default Bucket**: Your bucket name
6. Click **Save & Test**

### 4. Import Dashboard

1. In Grafana, go to **Dashboards** → **Import**
2. Click **Upload JSON file**
3. Select `ffxiv-fc-economy-dashboard.json`
4. Or copy-paste the JSON content
5. Select your InfluxDB data source from the dropdown
6. Click **Import**

### 5. Verify Data

1. Wait 1-2 minutes for initial data collection
2. Check the dashboard - you should see:
   - Gil totals
   - Character counts
   - Recent data points
3. If no data appears:
   - Verify InfluxReborn is running in FFXIV
   - Check InfluxDB for data: `SHOW MEASUREMENTS`
   - Verify time range in Grafana (default: last 7 days)

## Configuration Options

### Time Range

Adjust the time range in the top-right corner:
- Last 5 minutes (for testing)
- Last 24 hours (daily monitoring)
- Last 7 days (default)
- Last 30 days (monthly review)

### Variables/Filters

Use the dropdowns at the top to filter:
- **World**: Select specific FFXIV server
- **Entity Type**: Filter by Character/Retainer/FC
- **Free Company**: Filter by FC name
- **Item Filter**: Filter inventory items

### Auto-Refresh

Set auto-refresh in the top-right corner:
- Recommended: 5 minutes (default)
- For active monitoring: 1 minute
- For historical review: Off

## Data Collection

### Initial Setup

After installing InfluxReborn, data collection happens automatically:

1. **Every 1 minute**: While logged in
2. **On logout**: Final snapshot
3. **Manual trigger**: `/influx gil` command

### What's Collected

- ✅ Character gil, MGP, ventures
- ✅ Retainer inventories
- ✅ FC chest contents
- ✅ Repair kits and ceruleum tanks
- ✅ Grand Company seals
- ✅ Job levels (with AllaganTools)
- ✅ Inventory items (with AllaganTools filters)
- ✅ Submarine data (with SubmarineTracker)

### Character Enrollment

Enable in InfluxReborn settings:
- **Auto-enroll characters**: Automatically track new characters on login
- **Manual enrollment**: Select characters to track in plugin settings
- **FC tracking**: Enable per-character to track FC chest data

## Troubleshooting

### No Data in Dashboard

```bash
# Check InfluxDB has data
docker exec -it influxdb influx

# In InfluxDB CLI
> SHOW DATABASES
> USE your_bucket_name
> SHOW MEASUREMENTS
> SELECT * FROM currency LIMIT 10
```

### Connection Issues

1. **InfluxReborn → InfluxDB**:
   - Verify server URL is correct
   - Check firewall rules
   - Confirm API token is valid
   - Test connection in plugin settings

2. **Grafana → InfluxDB**:
   - Verify data source configuration
   - Check network connectivity
   - Review Grafana logs: `docker logs grafana`

### Missing Panels

Some panels require specific plugins:
- **Inventory items**: Requires AllaganTools
- **Submarines**: Requires SubmarineTracker
- **Class levels**: Requires AllaganTools

### Performance Issues

If dashboard is slow:
1. Reduce time range (e.g., last 24h instead of 30d)
2. Use variables to filter data
3. Increase refresh interval
4. Check InfluxDB query performance
5. Consider data retention policies

## Advanced Configuration

### InfluxDB Retention Policy

Keep data manageable:

```sql
-- Keep detailed data for 30 days
CREATE RETENTION POLICY "30_days" ON "your_db" DURATION 30d REPLICATION 1

-- Downsample to hourly for 1 year
CREATE RETENTION POLICY "1_year" ON "your_db" DURATION 365d REPLICATION 1

-- Create continuous query for downsampling
CREATE CONTINUOUS QUERY "cq_hourly" ON "your_db"
BEGIN
  SELECT mean(*) INTO "1_year".:MEASUREMENT FROM "30_days"./.*/ 
  GROUP BY time(1h), *
END
```

### Multiple FFXIV Installations

If managing multiple game installations:
1. Use different InfluxDB buckets per installation
2. Or use tags to distinguish (configure in InfluxReborn)
3. Create separate dashboards or use variables

### Backup

#### Export Dashboard

```bash
# From Grafana UI: Dashboard Settings → JSON Model → Copy
# Or use API:
curl -H "Authorization: Bearer YOUR_API_KEY" \
  http://localhost:3000/api/dashboards/uid/ffxiv-fc-economy
```

#### Backup InfluxDB

```bash
# InfluxDB 2.x
influx backup /path/to/backup

# Using Docker
docker exec influxdb influx backup /backup
docker cp influxdb:/backup ./backup
```

## Support Resources

- **InfluxReborn**: Check plugin documentation in Dalamud
- **Grafana Docs**: https://grafana.com/docs/
- **InfluxDB Docs**: https://docs.influxdata.com/
- **QuestDB Docs**: https://questdb.io/docs/

## Next Steps

1. ✅ Verify data collection is working
2. ✅ Customize thresholds for your needs
3. ✅ Set up alerts (Grafana Alerting)
4. ✅ Create additional panels for custom metrics
5. ✅ Share dashboard with FC members (with appropriate permissions)

---

**Need Help?** Review the main [README.md](README.md) for detailed dashboard features and customization options.
