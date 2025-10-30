# Example Queries for FFXIV Dashboard

This document contains useful InfluxQL queries for customizing your dashboard or creating new panels.

## Basic Currency Queries

### Total Gil Across All Entities

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE $timeFilter 
GROUP BY time(1h) fill(null)
```

### Gil by Entity Type

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE $timeFilter 
GROUP BY time($__interval), "type" fill(null)
```

### Gil by Specific World

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE $timeFilter AND "world" = 'Balmung'
GROUP BY time(1h) fill(null)
```

### Character Count

```sql
SELECT count(DISTINCT("id")) 
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
```

## Free Company Queries

### Top 10 FCs by Gil

```sql
SELECT sum("gil") as "gil"
FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY "fc_name", "world"
ORDER BY "gil" DESC 
LIMIT 10
```

### FC Credits Total

```sql
SELECT sum("fccredit") 
FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY time(1h) fill(null)
```

### FCs with Low Resources

```sql
SELECT last("gil"), last("repair_kits"), last("ceruleum_tanks")
FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY "fc_name", "world"
HAVING last("gil") < 1000000 OR 
       last("repair_kits") < 50 OR 
       last("ceruleum_tanks") < 20
```

## Character Queries

### Characters with Most Gil

```sql
SELECT last("gil") as "gil"
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY "player_name", "world"
ORDER BY "gil" DESC 
LIMIT 20
```

### Average Gil per Character

```sql
SELECT mean("gil") 
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY time(1h) fill(null)
```

### Characters by World

```sql
SELECT count(DISTINCT("id"))
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY "world"
```

### MGP Ranking

```sql
SELECT last("mgp") as "mgp"
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY "player_name", "world"
ORDER BY "mgp" DESC 
LIMIT 20
```

## Retainer Queries

### Total Retainer Gil

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE "type" = 'Retainer' AND $timeFilter
GROUP BY time(1h) fill(null)
```

### Retainers per Character

```sql
SELECT count(DISTINCT("id"))
FROM "currency" 
WHERE "type" = 'Retainer' AND $timeFilter
GROUP BY "player_name"
```

### Retainer Resources by Owner

```sql
SELECT sum("gil"), sum("repair_kits"), sum("ceruleum_tanks")
FROM "currency" 
WHERE "type" = 'Retainer' AND $timeFilter
GROUP BY "player_name", "world"
```

## Inventory Queries

### Search for Specific Item

```sql
SELECT sum("quantity") as "quantity", sum("total_gil") as "value"
FROM "items" 
WHERE $timeFilter AND "item_name" =~ /Potion/
GROUP BY "item_name", "filter_name"
```

### Most Valuable Items

```sql
SELECT sum("total_gil") as "value"
FROM "items" 
WHERE $timeFilter
GROUP BY "item_name"
ORDER BY "value" DESC 
LIMIT 30
```

### Items by Category

```sql
SELECT sum("quantity") as "quantity"
FROM "items" 
WHERE $timeFilter
GROUP BY "filter_name"
```

### HQ vs NQ Items

```sql
SELECT sum("quantity") as "quantity"
FROM "items" 
WHERE $timeFilter
GROUP BY "hq"
```

### Item Distribution Across Characters

```sql
SELECT sum("quantity") as "quantity"
FROM "items" 
WHERE $timeFilter AND "item_name" = 'Grade 8 Dark Matter'
GROUP BY "filter_name"
```

### Items Running Low

```sql
SELECT last("quantity") as "quantity"
FROM "items" 
WHERE $timeFilter AND "item_name" IN ('Dark Matter', 'Ceruleum Tank')
GROUP BY "item_name"
HAVING last("quantity") < 100
```

## Submarine Queries

### Total Active Submarines

```sql
SELECT count("level") 
FROM "submersibles" 
WHERE "enabled" = 1 AND $timeFilter
```

### Average Submarine Level

```sql
SELECT mean("level"), max("level"), min("level")
FROM "submersibles" 
WHERE $timeFilter
GROUP BY time(1h) fill(null)
```

### Submarines by FC

```sql
SELECT count("level"), mean("level")
FROM "submersibles" 
WHERE $timeFilter
GROUP BY "fc_name", "world"
```

### Submarines by Build Type

```sql
SELECT count("level") 
FROM "submersibles" 
WHERE $timeFilter
GROUP BY "build"
```

### Low Level Submarines

```sql
SELECT last("level") as "level"
FROM "submersibles" 
WHERE $timeFilter
GROUP BY "fc_name", "sub_name", "world"
HAVING last("level") < 50
```

### Available Submarine Slots

```sql
SELECT sum("free_slots") 
FROM "unbuilt_submersibles" 
WHERE $timeFilter
```

### Submarines Returning Soon

```sql
SELECT last("return_time") as "return_time"
FROM "submersibles" 
WHERE $timeFilter AND "state" != 0
GROUP BY "fc_name", "sub_name", "world"
ORDER BY "return_time" ASC 
LIMIT 20
```

## Resource Distribution Queries

### Repair Kits by Type

```sql
SELECT sum("repair_kits") 
FROM "currency" 
WHERE $timeFilter
GROUP BY time($__interval), "type" fill(null)
```

### Ceruleum Distribution

```sql
SELECT sum("ceruleum_tanks") 
FROM "currency" 
WHERE $timeFilter
GROUP BY "type"
```

### Ventures Distribution

```sql
SELECT sum("ventures") 
FROM "currency" 
WHERE $timeFilter
GROUP BY "type"
```

## Regional Analysis Queries

### Gil by World

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE $timeFilter
GROUP BY "world"
ORDER BY sum("gil") DESC
```

### Resources per World

```sql
SELECT 
  sum("gil") as "gil",
  sum("repair_kits") as "repair_kits",
  sum("ceruleum_tanks") as "ceruleum_tanks"
FROM "currency" 
WHERE $timeFilter
GROUP BY "world"
```

### Character Count by World

```sql
SELECT count(DISTINCT("id"))
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY "world"
```

### FC Count by World

```sql
SELECT count(DISTINCT("id"))
FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY "world"
```

## Time-based Analysis

### Daily Gil Change

```sql
SELECT derivative(sum("gil"), 1d) 
FROM "currency" 
WHERE $timeFilter
GROUP BY time(1d) fill(null)
```

### Weekly Growth Rate

```sql
SELECT difference(sum("gil"))
FROM "currency" 
WHERE $timeFilter
GROUP BY time(1w) fill(null)
```

### Peak Gil Time

```sql
SELECT max(sum("gil"))
FROM "currency" 
WHERE $timeFilter
GROUP BY time(1d)
```

## Advanced Queries

### Gil per Character Average by World

```sql
SELECT sum("gil") / count(DISTINCT("id")) as "avg_gil_per_char"
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY "world"
```

### Resource Efficiency Score (Gil / Repair Kits ratio)

```sql
SELECT sum("gil") / (sum("repair_kits") + 1) as "efficiency"
FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY "fc_name", "world"
ORDER BY "efficiency" DESC
LIMIT 20
```

### Submarines per FC

```sql
SELECT count("level") as "sub_count"
FROM "submersibles" 
WHERE $timeFilter
GROUP BY "fc_name"
ORDER BY "sub_count" DESC
```

### Total Inventory Value

```sql
SELECT sum("total_gil") as "total_value"
FROM "items" 
WHERE $timeFilter
GROUP BY time(1h) fill(null)
```

## Alerting Queries

### FCs Running Low on Resources

```sql
SELECT last("gil"), last("repair_kits"), last("ceruleum_tanks")
FROM "currency" 
WHERE "type" = 'FreeCompanyChest' AND $timeFilter
GROUP BY "fc_name"
HAVING last("gil") < 500000 OR 
       last("repair_kits") < 20 OR 
       last("ceruleum_tanks") < 10
```

### Characters Hitting Gil Cap

```sql
SELECT last("gil")
FROM "currency" 
WHERE "type" = 'Character' AND $timeFilter
GROUP BY "player_name", "world"
HAVING last("gil") > 990000000
```

### Submarines at Max Level

```sql
SELECT count("level")
FROM "submersibles" 
WHERE "level" = 100 AND $timeFilter
GROUP BY "fc_name"
```

## Using Variables in Queries

### With World Variable

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE $timeFilter AND "world" =~ /^$world$/
GROUP BY time(1h) fill(null)
```

### With Entity Type Variable

```sql
SELECT sum("gil") 
FROM "currency" 
WHERE $timeFilter AND "type" =~ /^$entity_type$/
GROUP BY time($__interval) fill(null)
```

### With FC Name Variable

```sql
SELECT sum("gil"), sum("fccredit"), sum("repair_kits")
FROM "currency" 
WHERE $timeFilter AND "fc_name" =~ /^$fc_name$/
GROUP BY time(1h) fill(null)
```

### With Item Filter Variable

```sql
SELECT sum("quantity"), sum("total_gil")
FROM "items" 
WHERE $timeFilter AND "filter_name" =~ /^$item_filter$/
GROUP BY "item_name"
```

## Tips for Query Optimization

1. **Use time filters**: Always include `$timeFilter` or `WHERE time > now() - 7d`
2. **Aggregate appropriately**: Use `sum()`, `mean()`, `count()` instead of raw data
3. **Limit results**: Add `LIMIT` clause for large datasets
4. **Use fill()**: Handle gaps with `fill(null)` or `fill(previous)`
5. **Group by time**: Use `GROUP BY time($__interval)` for automatic interval
6. **Index tags**: Ensure frequently queried tags are properly indexed
7. **Use continuous queries**: Pre-aggregate data for common queries
8. **Cache results**: Enable Grafana query caching for slow queries

## Creating New Panels

To create a new panel with these queries:

1. Edit dashboard
2. Add new panel
3. Select your InfluxDB data source
4. Choose query language (InfluxQL)
5. Paste and modify query from above
6. Adjust visualization type
7. Configure display options
8. Save panel

## Debugging Queries

If a query isn't working:

1. Test in InfluxDB CLI or UI first
2. Check measurement and field names
3. Verify time range has data
4. Review Grafana query inspector
5. Check for typos in tag names
6. Ensure data types match (string vs number)

---

**Note**: Replace `$timeFilter`, `$__interval`, and template variables like `$world` with actual values when testing in InfluxDB CLI.
