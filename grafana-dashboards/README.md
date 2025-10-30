# FFXIV Free Company Economy Dashboard

This directory contains Grafana dashboards for visualizing Final Fantasy XIV game statistics collected by the InfluxReborn plugin.

## 📊 Main Dashboard: `ffxiv-fc-economy-dashboard.json`

A comprehensive, production-ready dashboard designed for managing **300+ Free Companies** and **1000+ characters** across multiple worlds/data centers.

### Features

#### 🎯 Executive Summary
- **Complete Totals**: Real-time totals for Gil, Repair Kits, Ceruleum Tanks, and FC Credits
- **Trend Analysis**: Historical trends showing wealth accumulation over time
- **Visual Indicators**: Color-coded stats with threshold-based alerts

#### 🌍 Regional Breakdown
- **By World**: Distribution of resources across all FFXIV worlds/servers
- **Comparative Analysis**: Side-by-side comparison of resources by region
- **Interactive Tables**: Sortable and filterable regional summaries

#### 🏰 Free Company Management
- **Top Performers**: Top 20 FCs ranked by total Gil
- **Searchable FC List**: Complete list of all 300+ Free Companies with filtering
- **Resource Tracking**: Gil, FC Credits, Repair Kits, and Ceruleum Tanks per FC
- **Trend Monitoring**: Track FC wealth changes over time

#### 👤 Character & Retainer Management
- **Wealth Rankings**: Top 20 richest characters
- **Character Search**: Filterable list of all 1000+ characters
- **Retainer Inventory**: Track resources stored on retainers
- **Owner Association**: Link retainers to their character owners

#### 📦 Inventory Management & Item Search
- **Global Item Search**: Search across all characters, retainers, and FC chests
- **Value Tracking**: Monitor total gil value of inventory items
- **Category Analysis**: Items grouped by AllaganTools filters
- **Top Items**: Identify most valuable items in your economy
- **Quantity Tracking**: Track item quantities across the entire network

#### 🚢 Submarine Fleet Management
- **Fleet Overview**: Total submarines, active subs, average levels
- **Capacity Planning**: Track available submarine slots
- **Detailed Status**: Level, build, and voyage status for each submarine
- **FC Association**: Link submarines to their Free Companies

#### 📈 Economic Distribution & Analytics
- **Wealth Distribution**: Analyze how gil is distributed across entity types
- **Resource Trends**: Track repair kits and ceruleum tanks over time
- **Stacked Analytics**: See cumulative resource growth
- **Summary Tables**: Comprehensive breakdowns by entity type

### Dashboard Sections

1. **Executive Summary** - High-level KPIs and totals
2. **Regional Breakdown** - World/server-based analysis
3. **Free Company Management** - FC-specific metrics
4. **Character & Retainer Management** - Individual character tracking
5. **Inventory Management** - Item search and value tracking
6. **Submarine Fleet Management** - Submarine tracking and optimization
7. **Economic Distribution** - Resource distribution analytics

### Variables/Filters

The dashboard includes the following template variables for filtering:

- **World**: Filter by FFXIV world/server
- **Entity Type**: Filter by Character, Retainer, or FreeCompanyChest
- **Free Company**: Filter by specific FC name
- **Item Filter**: Filter inventory by AllaganTools filter name

### Key Metrics Tracked

| Metric | Source | Description |
|--------|--------|-------------|
| Gil | `currency` measurement | Total gil across all entities |
| Repair Kits | `currency` measurement | Dark Matter repair kits |
| Ceruleum Tanks | `currency` measurement | Fuel for airships/submarines |
| FC Credits | `currency` measurement | Free Company credits |
| Ventures | `currency` measurement | Retainer venture currency |
| MGP | `currency` measurement | Manderville Gold Saucer Points |
| Items | `items` measurement | Inventory items with quantities and values |
| Submarines | `submersibles` measurement | Submarine fleet data |

## 📥 Installation

### Prerequisites

1. **InfluxDB or QuestDB** instance running
2. **Grafana** instance (v9.0 or later recommended)
3. **InfluxReborn** plugin installed in FFXIV
4. **AllaganTools** plugin for inventory tracking (optional but recommended)

### Import Steps

1. Open your Grafana instance
2. Navigate to **Dashboards** → **Import**
3. Upload `ffxiv-fc-economy-dashboard.json` or paste its contents
4. Select your InfluxDB/QuestDB data source
5. Click **Import**

### Data Source Configuration

The dashboard expects an InfluxDB or QuestDB data source with the following measurements:

- `currency` - Character, Retainer, and FC currency data
- `items` - Inventory item tracking
- `submersibles` - Submarine fleet data
- `unbuilt_submersibles` - Available submarine slots
- `grandcompany` - Grand Company data
- `experience` - Job levels
- `quests` - Quest progress
- `retainer` - Retainer-specific data

## 🎨 Customization

### Thresholds

You can customize color thresholds for various metrics:

- **Gil Thresholds**: Default at 1M (yellow), 10M (green), 100M (blue)
- **Repair Kit Thresholds**: Default at 100 (yellow), 500 (green)
- **Ceruleum Tank Thresholds**: Default at 50 (yellow), 200 (green)
- **Submarine Level Thresholds**: Default at 50 (yellow), 80 (green)

### Refresh Rate

Default refresh rate is **5 minutes**. Adjust in dashboard settings if needed:
- Settings → Time options → Auto refresh

### Time Range

Default time range is **Last 7 days**. Common alternatives:
- Last 24 hours - For daily monitoring
- Last 30 days - For monthly trends
- Last 90 days - For quarterly analysis

## 🔍 Use Cases

### For FC Leaders
- Monitor total FC wealth across all Free Companies
- Track resource distribution to identify imbalances
- Identify FCs needing support or redistribution
- Monitor submarine fleet efficiency

### For Economy Managers
- Track total network wealth (1000+ characters)
- Identify hoarding or resource bottlenecks
- Monitor item value trends
- Plan resource redistribution strategies

### For Individual Players
- Search for specific items across all storage
- Track personal wealth growth over time
- Monitor retainer inventories
- Compare with network averages

### For Alliance Management
- Compare performance across worlds/data centers
- Identify regional economic trends
- Balance resources between servers
- Coordinate large-scale economic strategies

## 📊 Query Examples

### Custom Gil Query
```sql
SELECT sum("gil") FROM "currency" 
WHERE $timeFilter AND "world" = 'YourWorld'
GROUP BY time($__interval), "type" fill(null)
```

### Search for Specific Item
```sql
SELECT sum("quantity"), sum("total_gil") FROM "items" 
WHERE $timeFilter AND "item_name" =~ /YourItemName/
GROUP BY "item_name", "filter_name"
```

### Top FC by Credits
```sql
SELECT sum("fccredit") FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY "fc_name" ORDER BY sum("fccredit") DESC LIMIT 10
```

## 🚀 Performance Tips

1. **Use Time Filters**: Limit queries to relevant time ranges
2. **Enable Pagination**: Tables support pagination for large datasets
3. **Use Variables**: Leverage template variables to filter data
4. **Optimize Refresh**: Set appropriate auto-refresh intervals
5. **Index Tags**: Ensure InfluxDB tags are properly indexed

## 🐛 Troubleshooting

### No Data Showing
- Verify InfluxReborn plugin is running and configured
- Check data source connection in Grafana
- Ensure time range includes data collection period
- Verify measurement names match your InfluxDB schema

### Slow Performance
- Reduce time range for queries
- Use template variables to filter data
- Check InfluxDB query performance
- Consider data retention policies

### Missing Panels
- Verify all required measurements exist in your database
- Check that plugins (AllaganTools, SubmarineTracker) are installed
- Review browser console for errors

## 📝 Data Schema

### Currency Measurement
```
Tags: id, player_name, world, type, fc_id, fc_name, retainer_name
Fields: gil, mgp, ventures, ceruleum_tanks, repair_kits, fccredit, free_inventory
```

### Items Measurement
```
Tags: filter_name, item_id, item_name, hq
Fields: quantity, total_gil
```

### Submersibles Measurement
```
Tags: id, world, fc_name, sub_id, sub_name, part_hull, part_stern, part_bow, part_bridge, build
Fields: enabled, level, predicted_level, state, return_time
```

## 🔐 Security Considerations

- **Access Control**: Restrict dashboard access to authorized users
- **Data Privacy**: Contains character names and wealth information
- **API Keys**: Secure InfluxDB credentials properly
- **Network**: Use HTTPS for Grafana and InfluxDB connections

## 📄 License

This dashboard configuration is provided as-is for use with the InfluxReborn plugin. Modify and distribute freely.

## 🤝 Contributing

To improve this dashboard:
1. Make modifications in Grafana UI
2. Export updated JSON
3. Submit changes with clear documentation
4. Test with multiple data sources

## 📞 Support

For issues or questions:
- Check the InfluxReborn plugin documentation
- Review Grafana documentation for query syntax
- Verify InfluxDB/QuestDB setup and data collection

---

**Version**: 1.0  
**Last Updated**: 2025-10-30  
**Compatible With**: Grafana 9.0+, InfluxDB 1.8+/2.0+, QuestDB 6.0+
