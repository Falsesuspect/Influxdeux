# Using the Dashboard with QuestDB

This guide explains how to use the FFXIV FC Economy Dashboard with **QuestDB** instead of InfluxDB.

## Quick Start

### 1. Dashboard Selection

Use the **QuestDB-specific dashboard**:
- **File**: `ffxiv-fc-economy-dashboard-questdb.json`
- **Queries**: PostgreSQL-compatible SQL (not InfluxQL)
- **Data Source**: PostgreSQL (for QuestDB wire protocol)

### 2. QuestDB Setup

#### Using Docker (Recommended)

```bash
docker run -d \
  --name questdb \
  -p 9000:9000 \
  -p 8812:8812 \
  -p 9009:9009 \
  -v questdb-data:/var/lib/questdb \
  questdb/questdb:latest
```

**Ports:**
- `9000` - Web console
- `8812` - PostgreSQL wire protocol (for Grafana)
- `9009` - InfluxDB line protocol (for InfluxReborn plugin)

Access the QuestDB web console at: `http://localhost:9000`

### 3. Configure InfluxReborn Plugin

In FFXIV, configure the plugin to use QuestDB:

```
/influx
```

1. Select **QuestDB** as database type
2. Enter server URL: `http://localhost:9009` (or your QuestDB ILP port)
3. Leave username/password empty (unless you configured authentication)
4. Test connection
5. Save and enable characters

### 4. Set Up Grafana

#### Configure PostgreSQL Data Source for QuestDB

1. Open Grafana at `http://localhost:3000`
2. Go to **Configuration** → **Data Sources** → **Add data source**
3. Select **PostgreSQL**
4. Configure:
   - **Name**: QuestDB (or any name you prefer)
   - **Host**: `localhost:8812`
   - **Database**: `qdb` (default QuestDB database)
   - **User**: `admin` (default, or your configured user)
   - **Password**: `quest` (default, or your configured password)
   - **SSL Mode**: `disable` (unless you configured SSL)
   - **Version**: 12+ (QuestDB is compatible with PostgreSQL 12+)
5. Click **Save & Test** - should show "Database Connection OK"

### 5. Import QuestDB Dashboard

1. In Grafana, go to **Dashboards** → **Import**
2. Click **Upload JSON file**
3. Select `ffxiv-fc-economy-dashboard-questdb.json`
4. Select your PostgreSQL data source (configured for QuestDB)
5. Click **Import**

## Key Differences from InfluxDB Version

### Query Syntax

The QuestDB dashboard uses SQL instead of InfluxQL:

| InfluxQL | QuestDB SQL |
|----------|-------------|
| `SELECT sum("gil") FROM "currency"` | `SELECT sum(gil) FROM currency` |
| `WHERE $timeFilter` | `WHERE timestamp BETWEEN $__timeFrom()::timestamp AND $__timeTo()::timestamp` |
| `GROUP BY time(1h) fill(null)` | `SAMPLE BY 1h` |
| `GROUP BY time($__interval), "tag"` | `GROUP BY tag SAMPLE BY $__interval` |
| `last("field")` | `last(field)` |
| `mean("field")` | `AVG(field)` |

### Data Source Type

- **InfluxDB Version**: Uses `influxdb` data source type
- **QuestDB Version**: Uses `postgres` data source type

### Template Variables

The QuestDB dashboard uses PostgreSQL-compatible queries for variables:

```sql
-- Get distinct worlds
SELECT DISTINCT world FROM currency WHERE world IS NOT NULL

-- Get distinct Free Companies
SELECT DISTINCT fc_name FROM currency WHERE fc_name IS NOT NULL

-- Get distinct item filters
SELECT DISTINCT filter_name FROM items WHERE filter_name IS NOT NULL
```

## Data Collection

### InfluxReborn with QuestDB

The InfluxReborn plugin sends data to QuestDB using the **InfluxDB Line Protocol (ILP)**:
- Port: `9009` (default QuestDB ILP port)
- Protocol: ILP over TCP or HTTP
- Same measurements and fields as InfluxDB version

### Table Structure

QuestDB automatically creates tables from ILP data:

```sql
-- View available tables
SHOW TABLES;

-- View currency table structure
SHOW COLUMNS FROM currency;

-- Sample data
SELECT * FROM currency LIMIT 10;
```

### Timestamp Handling

QuestDB uses `timestamp` as the designated timestamp column:
- Automatically created from ILP data
- Indexed for fast time-series queries
- Used in `SAMPLE BY` clauses

## Performance Tuning

### QuestDB-Specific Optimizations

1. **Partitioning**: QuestDB partitions tables by time automatically
   ```sql
   -- Check partition info
   SELECT * FROM table_partitions('currency');
   ```

2. **Deduplication**: Enable if you need unique records
   ```sql
   ALTER TABLE currency DEDUP ENABLE UPSERT KEYS(timestamp, id);
   ```

3. **Column Indexing**: Index frequently queried symbol columns
   ```sql
   -- Indexes are created automatically for symbol columns
   -- from ILP protocol (world, type, fc_name, etc.)
   ```

### Query Performance Tips

1. Use `SAMPLE BY` for time-series aggregations (faster than GROUP BY)
2. Filter by timestamp first for best performance
3. Use `LATEST ON` for getting most recent values per entity
4. Leverage symbol columns (tags) for grouping

## Advanced QuestDB Features

### Latest On Queries

Get most recent values efficiently:

```sql
-- Latest gil per character
SELECT gil, player_name, world
FROM currency
WHERE type = 'Character'
LATEST ON timestamp PARTITION BY id;
```

### Sample By with Fill

```sql
-- Fill gaps with previous values
SELECT timestamp, sum(gil)
FROM currency
WHERE timestamp > dateadd('d', -7, now())
SAMPLE BY 1h FILL(prev);
```

### Window Functions

```sql
-- Calculate rolling averages
SELECT timestamp, gil,
       AVG(gil) OVER (PARTITION BY player_name ORDER BY timestamp ROWS BETWEEN 10 PRECEDING AND CURRENT ROW) as moving_avg
FROM currency
WHERE type = 'Character';
```

## Troubleshooting

### Connection Issues

**Problem**: Grafana can't connect to QuestDB
- **Solution**: Verify port 8812 is accessible
- Check QuestDB is running: `docker ps | grep questdb`
- Test with psql: `psql -h localhost -p 8812 -U admin -d qdb`

**Problem**: Plugin can't send data to QuestDB
- **Solution**: Verify port 9009 is accessible
- Check QuestDB ILP logs: `docker logs questdb`
- Test with telnet: `telnet localhost 9009`

### Query Errors

**Problem**: "timestamp column not found"
- **Solution**: QuestDB needs ILP data first. Ensure InfluxReborn is sending data.

**Problem**: "SAMPLE BY requires designated timestamp"
- **Solution**: Use `SAMPLE BY` on tables with timestamp columns only.

**Problem**: "Invalid syntax near 'fill'"
- **Solution**: Use `FILL(null)` or `FILL(prev)` syntax, not `fill(null)`.

### Performance Issues

**Problem**: Queries are slow
- **Solution**: 
  - Add WHERE clause for timestamp filtering
  - Use `SAMPLE BY` instead of `GROUP BY` for time-series
  - Check partition strategy
  - Use `EXPLAIN` to analyze query plan

**Problem**: Dashboard takes long to load
- **Solution**:
  - Reduce time range
  - Increase auto-refresh interval
  - Use pagination on large tables
  - Consider data retention policies

## Data Retention

Configure retention in QuestDB:

```sql
-- Drop partitions older than 30 days
ALTER TABLE currency DROP PARTITION 
WHERE timestamp < dateadd('d', -30, now());
```

Or use scheduled jobs to clean old data automatically.

## Backup and Recovery

### Backup QuestDB Data

```bash
# Stop QuestDB
docker stop questdb

# Backup data directory
docker cp questdb:/var/lib/questdb ./questdb-backup

# Restart QuestDB
docker start questdb
```

### Restore from Backup

```bash
# Stop QuestDB
docker stop questdb

# Restore data
docker cp ./questdb-backup questdb:/var/lib/questdb

# Restart QuestDB
docker start questdb
```

## Migration from InfluxDB

If migrating from InfluxDB to QuestDB:

1. **Export data from InfluxDB**:
   ```bash
   influx backup /path/to/backup
   ```

2. **Convert to ILP format** (script needed)

3. **Import to QuestDB**:
   ```bash
   cat data.ilp | nc localhost 9009
   ```

Or run both systems in parallel and let InfluxReborn send to both.

## Resources

- **QuestDB Documentation**: https://questdb.io/docs/
- **QuestDB SQL Reference**: https://questdb.io/docs/reference/sql/
- **PostgreSQL Wire Protocol**: https://questdb.io/docs/reference/api/postgres/
- **InfluxDB Line Protocol**: https://questdb.io/docs/reference/api/ilp/
- **Grafana PostgreSQL**: https://grafana.com/docs/grafana/latest/datasources/postgres/

## Example Queries for QuestDB

### Total Gil by Entity Type

```sql
SELECT type, sum(gil) as total_gil
FROM currency
WHERE timestamp > dateadd('d', -1, now())
GROUP BY type;
```

### Top 10 Characters by Gil

```sql
SELECT player_name, world, gil
FROM currency
WHERE type = 'Character'
LATEST ON timestamp PARTITION BY id
ORDER BY gil DESC
LIMIT 10;
```

### Gil Trend (Last 7 Days)

```sql
SELECT timestamp, sum(gil) as total_gil
FROM currency
WHERE timestamp > dateadd('d', -7, now())
SAMPLE BY 1h;
```

### Free Companies with Low Resources

```sql
SELECT fc_name, world, gil, repair_kits, ceruleum_tanks
FROM currency
WHERE type = 'FreeCompanyChest'
  AND (gil < 1000000 OR repair_kits < 50 OR ceruleum_tanks < 20)
LATEST ON timestamp PARTITION BY id;
```

---

**Note**: The QuestDB dashboard provides the same functionality as the InfluxDB version but uses PostgreSQL-compatible SQL for better performance and QuestDB-specific features.
