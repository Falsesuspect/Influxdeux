# InfluxReborn (Influxdeux)

A Dalamud plugin for Final Fantasy XIV that uploads game statistics to InfluxDB or QuestDB, enabling powerful analytics and visualizations through tools like Grafana.

## Overview

InfluxReborn collects and synchronizes game data including:
- 💰 Currency (Gil, MGP, Ventures, FC Credits)
- 🔧 Resources (Repair Kits, Ceruleum Tanks)
- 📦 Inventory Items (via AllaganTools integration)
- 🚢 Submarine Fleet (via SubmarineTracker integration)
- ⚔️ Character Stats (Levels, Grand Company, Quests)
- 🏰 Free Company Data

## Features

- **Real-time Data Collection**: Automatic updates every minute while logged in
- **Multi-Character Support**: Track unlimited characters across all worlds
- **FC Management**: Monitor Free Company resources and economy
- **Flexible Storage**: Compatible with InfluxDB and QuestDB
- **Privacy Controls**: Select which characters to track
- **Low Overhead**: Minimal performance impact on game

## Quick Start

### Installation

1. Install [Dalamud](https://github.com/goatcorp/Dalamud)
2. Add this repository to your Dalamud plugin sources
3. Install "InfluxReborn" from the plugin installer
4. Configure your database connection: `/influx`

### Configuration

```
/influx
```

Set up your database connection:
- Choose InfluxDB or QuestDB
- Enter server URL
- Configure authentication
- Test connection
- Enable characters to track

## Grafana Dashboard

This repository includes **two versions** of a comprehensive Grafana dashboard designed for managing **300+ Free Companies** and **1000+ characters**:

- **InfluxDB Version** (`ffxiv-fc-economy-dashboard.json`): Uses InfluxQL queries
- **QuestDB Version** (`ffxiv-fc-economy-dashboard-questdb.json`): Uses PostgreSQL-compatible SQL

### 📊 Dashboard Features

- **Complete Totals**: Gil, Repair Kits, Ceruleum Tanks, FC Credits
- **Regional Breakdown**: Resources by world/data center
- **FC Management**: Rankings, trends, searchable lists
- **Character Tracking**: Individual character stats and wealth
- **Inventory Search**: Find items across all storage
- **Submarine Fleet**: Track and optimize submarine operations
- **Economic Analytics**: Distribution and trend analysis

### Quick Dashboard Setup

#### Using InfluxDB

```bash
# 1. Set up InfluxDB (using Docker)
docker run -d --name influxdb -p 8086:8086 influxdb:latest

# 2. Set up Grafana (using Docker)
docker run -d --name grafana -p 3000:3000 grafana/grafana:latest

# 3. Import dashboard
# Open Grafana at http://localhost:3000
# Go to Dashboards → Import
# Upload: grafana-dashboards/ffxiv-fc-economy-dashboard.json
```

#### Using QuestDB

```bash
# 1. Set up QuestDB (using Docker)
docker run -d --name questdb -p 9000:9000 -p 8812:8812 -p 9009:9009 questdb/questdb:latest

# 2. Set up Grafana (using Docker)
docker run -d --name grafana -p 3000:3000 grafana/grafana:latest

# 3. Import dashboard
# Open Grafana at http://localhost:3000
# Add PostgreSQL data source (host: localhost:8812, database: qdb)
# Go to Dashboards → Import
# Upload: grafana-dashboards/ffxiv-fc-economy-dashboard-questdb.json
```

See **[QUESTDB.md](grafana-dashboards/QUESTDB.md)** for detailed QuestDB setup.

### 📚 Dashboard Documentation

- **[README](grafana-dashboards/README.md)**: Complete dashboard documentation
- **[Installation Guide](grafana-dashboards/INSTALLATION.md)**: Step-by-step setup
- **[QuestDB Setup](grafana-dashboards/QUESTDB.md)**: QuestDB-specific guide
- **[Visual Preview](grafana-dashboards/PREVIEW.md)**: Dashboard layout reference
- **[Example Queries](grafana-dashboards/EXAMPLE-QUERIES.md)**: Custom query examples
- **[Features Summary](grafana-dashboards/FEATURES.md)**: Full feature list

### Dashboard Highlights

**28 Pre-built Panels** organized in 7 sections:
1. Executive Summary - KPIs and totals
2. Regional Breakdown - World-based analysis
3. Free Company Management - FC tracking
4. Character & Retainer Management - Individual stats
5. Inventory Management - Item search and valuation
6. Submarine Fleet Management - Fleet optimization
7. Economic Distribution - Resource analytics

## Data Schema

### Measurements

The plugin creates the following InfluxDB/QuestDB measurements:

#### `currency`
Main economic data for all entity types.

**Tags**: `id`, `player_name`, `world`, `type`, `fc_id`, `fc_name`, `retainer_name`  
**Fields**: `gil`, `mgp`, `ventures`, `ceruleum_tanks`, `repair_kits`, `fccredit`, `free_inventory`

#### `items`
Inventory item tracking (requires AllaganTools).

**Tags**: `filter_name`, `item_id`, `item_name`, `hq`  
**Fields**: `quantity`, `total_gil`

#### `submersibles`
Submarine fleet data (requires SubmarineTracker).

**Tags**: `id`, `world`, `fc_name`, `sub_name`, `build`  
**Fields**: `enabled`, `level`, `predicted_level`, `state`, `return_time`

#### `experience`
Character job levels.

**Tags**: `job`, `job_type`  
**Fields**: `level`

#### `grandcompany`
Grand Company affiliation and seals.

**Fields**: `gc`, `gc_rank`, `seals`, `seal_cap`, `squadron_unlocked`

#### `quests`
Main Scenario Quest progress.

**Tags**: `msq_name`  
**Fields**: `msq_count`, `msq_genre`

## Plugin Dependencies

### Required
- **Dalamud**: Plugin framework

### Optional (Enhanced Features)
- **AllaganTools**: Inventory tracking and item filters
- **SubmarineTracker**: Submarine fleet data

## Database Setup

### InfluxDB 2.x (Recommended)

```bash
# Docker setup
docker run -d \
  --name influxdb \
  -p 8086:8086 \
  -v influxdb-data:/var/lib/influxdb2 \
  influxdb:latest

# Initial setup
# Visit http://localhost:8086
# Create organization, bucket, and API token
```

### QuestDB

```bash
# Docker setup
docker run -d \
  --name questdb \
  -p 9000:9000 \
  -p 9009:9009 \
  -v questdb-data:/root/.questdb \
  questdb/questdb:latest

# Access UI: http://localhost:9000
```

## Use Cases

### For Individual Players
- Track personal wealth across all characters and retainers
- Monitor item inventory across storage
- Analyze income trends over time

### For FC Leaders
- Monitor FC chest resources
- Track member contributions
- Manage submarine operations
- Plan resource distribution

### For Multi-FC Managers
- **Manage 300+ Free Companies** across multiple worlds
- Track **1000+ characters** and their economies
- Identify resource imbalances
- Coordinate cross-world strategies
- Search items across entire network

## Commands

- `/influx` - Open configuration window
- `/influx gil` - Open statistics window and trigger update

## Configuration Options

### Auto-enroll Characters
Automatically start tracking new characters on first login.

### Include Free Company
Per-character setting to include FC chest data in tracking.

### Included Inventory Filters
Select AllaganTools filters to track for inventory items.

## Performance

- **Update Frequency**: Every 60 seconds while logged in
- **Network Overhead**: Minimal (< 1 KB per update)
- **CPU Impact**: Negligible (< 1% average)
- **Memory Usage**: ~5-10 MB

## Privacy & Security

- **Local Control**: You control what data is collected
- **Your Database**: Data stays on your own server
- **Configurable**: Enable/disable per character
- **Secure**: Supports authentication and HTTPS

## Troubleshooting

### No Data Appearing

1. Check InfluxReborn is enabled in Dalamud
2. Verify database connection: `/influx` → Test Connection
3. Confirm characters are enrolled in plugin settings
4. Check time range in Grafana (try "Last 5 minutes")

### Connection Failed

1. Verify database server is running
2. Check firewall rules allow connection
3. Confirm URL format (must start with http:// or https://)
4. Validate API token/credentials

### Missing Panels in Dashboard

Some panels require optional plugins:
- **Inventory panels**: Install AllaganTools
- **Submarine panels**: Install SubmarineTracker

## Contributing

Contributions welcome! Areas for improvement:
- Additional visualizations
- Query optimizations
- Documentation enhancements
- Bug fixes

## Credits

- **Original Influx Plugin**: [Liza Carvelli](https://git.carvel.li/liza/Influx)
- **InfluxReborn**: Melody Sabre
- **AllaganTools**: Critical Role
- **SubmarineTracker**: Community contribution
- **Grafana Dashboard**: Created for 300+ FC management

## License

This project is open source. See individual component licenses for details.

## Support

- **Plugin Issues**: Check Dalamud plugin support channels
- **Dashboard Questions**: Review documentation in `grafana-dashboards/`
- **Database Help**: Consult InfluxDB or QuestDB documentation

## Links

- **Plugin Repository**: This repository
- **Grafana**: https://grafana.com
- **InfluxDB**: https://www.influxdata.com
- **QuestDB**: https://questdb.io
- **Dalamud**: https://github.com/goatcorp/Dalamud

---

**Version**: 1.0  
**Compatible With**: FFXIV Dawntrail (7.x), Dalamud latest  
**Dashboard Panels**: 28  
**Supported Scale**: 300+ FCs, 1000+ characters
