# Dashboard Preview

## Visual Layout Overview

The FFXIV FC Economy Dashboard is organized into 7 main sections, each designed for specific monitoring needs.

## Section Breakdown

### 1. Executive Summary (Top of Dashboard)

```
┌─────────────────────────────────────────────────────────────────┐
│ 📊 Executive Summary - Complete Totals                          │
├────────────┬────────────┬────────────┬────────────┐             │
│ 💰 Total   │ 🔧 Total   │ ⛽ Total    │ 🏛️ Total   │             │
│ Gil        │ Repair Kits│ Ceruleum   │ FC Credits │             │
│            │            │ Tanks      │            │             │
│ 1.2B       │ 2,345      │ 1,891      │ 234,567    │             │
│ [Graph]    │ [Graph]    │ [Graph]    │ [Graph]    │             │
└────────────┴────────────┴────────────┴────────────┘             │
│                                                                  │
│ 💰 Gil Trend by Entity Type                                     │
│ [Line Graph showing Character/Retainer/FC trends over time]     │
└──────────────────────────────────────────────────────────────────┘
```

**Features**:
- Large stat panels with color-coded backgrounds
- Sparkline graphs showing trends
- Thresholds: Red < 1M < Yellow < 10M < Green < 100M < Blue

---

### 2. Regional Breakdown

```
┌─────────────────────────────────────────────────────────────────┐
│ 🌍 Regional Breakdown (By World)                                │
├──────────────────────────────┬──────────────────────────────────┤
│ 💰 Gil Distribution by World │ 🌍 Resources by World            │
│                              │                                  │
│     [Donut Chart]            │   World    │ Gil  │ Kits │ Tanks│
│                              │   ─────────┼──────┼──────┼──────│
│   Balmung: 35%               │   Balmung  │ 420M │ 890  │ 234 │
│   Gilgamesh: 28%             │   Gilga... │ 336M │ 456  │ 189 │
│   Hyperion: 22%              │   Hyperion │ 264M │ 234  │ 145 │
│   Others: 15%                │   ...      │ ...  │ ...  │ ... │
└──────────────────────────────┴──────────────────────────────────┘
│                                                                  │
│ 📊 Regional Resource Comparison                                 │
│ [Horizontal Bar Chart comparing Gil/Kits/Tanks by World]        │
└──────────────────────────────────────────────────────────────────┘
```

**Features**:
- Interactive donut chart for gil distribution
- Sortable table with all metrics
- Comparative bar chart (horizontal orientation)

---

### 3. Free Company Management

```
┌─────────────────────────────────────────────────────────────────┐
│ 🏰 Free Company Management                                      │
├──────────────────────────────┬──────────────────────────────────┤
│ 🏆 Top 20 FCs by Gil         │ 🔍 All Free Companies (Search)   │
│                              │                                  │
│ FC Name    │World│Gil│Credits│ [Search: ___________]           │
│ ───────────┼─────┼───┼───────│                                 │
│ Elite Squad│Balm │50M│12,345 │ FC Name    │World│Gil│Credits  │
│ Dragon Kin │Gilg │45M│10,234 │ ──────────┼─────┼───┼────────  │
│ Phoenix Res│Hyp  │42M│ 9,876 │ [All 300+ FCs with pagination]  │
│ ...        │...  │...│ ...   │                                 │
└──────────────────────────────┴──────────────────────────────────┘
│                                                                  │
│ 📈 FC Gil Trends Over Time                                      │
│ [Multi-line time series showing top FCs' gil trends]            │
└──────────────────────────────────────────────────────────────────┘
```

**Features**:
- Color-coded gil values (red for highest)
- Pagination for large datasets
- Real-time search/filter functionality
- Trend lines for each FC

---

### 4. Character & Retainer Management

```
┌─────────────────────────────────────────────────────────────────┐
│ 👤 Character & Retainer Management                              │
├──────────────────────────────┬──────────────────────────────────┤
│ 💎 Top 20 Characters by Gil  │ 🔍 All Characters (Searchable)   │
│                              │                                  │
│ Character │World│Gil │MGP│Ven│ [Search: ___________]           │
│ ──────────┼─────┼────┼───┼───│                                 │
│ John Doe  │Balm │20M │1.5M│99│ Character │World│Gil │MGP│Ven  │
│ Jane Smith│Gilg │18M │1.2M│99│ ─────────┼─────┼────┼───┼───  │
│ ...       │...  │... │...│.. │ [All 1000+ characters]          │
└──────────────────────────────┴──────────────────────────────────┘
│                                                                  │
│ 📦 Retainer Resources (Searchable)                              │
│ Retainer│Owner│World│Gil│Repair Kits│Ceruleum Tanks            │
│ ────────┼─────┼─────┼───┼───────────┼──────────────            │
│ [Searchable table with all retainer data and pagination]        │
└──────────────────────────────────────────────────────────────────┘
```

**Features**:
- Ranked character lists
- Full-text search on names
- Linked retainer → owner relationships
- Sortable by any column

---

### 5. Inventory Management & Item Search

```
┌─────────────────────────────────────────────────────────────────┐
│ 📦 Inventory Management & Item Search                           │
│                                                                  │
│ 🔍 All Inventory Items (Searchable & Filterable)                │
│ [Search: ___________] [Filter: ▼All Categories]                │
│                                                                  │
│ Item Name        │Filter  │ID  │HQ│Quantity│Total Value (Gil)  │
│ ─────────────────┼────────┼────┼──┼────────┼──────────────────  │
│ Grade 8 Dark Mat │Consumab│1234│1 │  2,345 │    23,450,000    │
│ Ceruleum Tank    │Consumab│5678│0 │  1,891 │     1,891,000    │
│ ...              │...     │... │..│    ... │          ...     │
│ [Pagination: 1 2 3 ... 50]                                      │
└──────────────────────────────────────────────────────────────────┘
│                                                                  │
│ 💰 Top 30 Most Valuable Items                                   │
│ [Horizontal Bar Chart showing items by total gil value]         │
│                                                                  │
├──────────────────────────────┬──────────────────────────────────┤
│ 📊 Items by Category/Filter  │ 💎 Inventory Value by Category   │
│ [Pie Chart showing quantity] │ [Donut Chart showing gil value]  │
└──────────────────────────────┴──────────────────────────────────┘
```

**Features**:
- **Real-time search** across all items
- **Filter by category** (AllaganTools filters)
- **Color-coded values** (high-value items highlighted)
- **HQ indicator** (High Quality items marked)
- **Multiple visualizations** (table, bar, pie, donut)

---

### 6. Submarine Fleet Management

```
┌─────────────────────────────────────────────────────────────────┐
│ 🚢 Submarine Fleet Management                                   │
├──────┬──────┬──────┬──────┐                                     │
│ 🚢   │ ⚓   │ 📊   │ 🆕   │                                     │
│Total │Active│ Avg  │Avail │                                     │
│ Subs │ Subs │Level │Slots │                                     │
│  847 │  723 │  87  │  124 │                                     │
└──────┴──────┴──────┴──────┘                                     │
│                                                                  │
│ 🚢 All Submarines (Searchable)                                  │
│ FC Name │World│Sub Name│Build │Status│Level│Pred.Lvl│State     │
│ ────────┼─────┼────────┼──────┼──────┼─────┼────────┼─────     │
│ Elite Sq│Balm │Sub-01  │Tataru│🟢    │ 100 │  100   │Voyage   │
│ Elite Sq│Balm │Sub-02  │Tataru│🟢    │  95 │   98   │Voyage   │
│ Dragon K│Gilg │Dragon-1│Custom│🟢    │  88 │   92   │Return   │
│ ...     │...  │...     │...   │...   │ ... │  ...   │...      │
└──────────────────────────────────────────────────────────────────┘
```

**Features**:
- **Quick stats** at a glance
- **Status indicators** (🟢 Active / 🔴 Inactive)
- **Level color coding** (red < 50 < yellow < 80 < green < 100 < blue)
- **Build tracking** (part configurations)
- **State monitoring** (Voyage, Return, Ready)

---

### 7. Economic Distribution & Analytics

```
┌─────────────────────────────────────────────────────────────────┐
│ 📈 Economic Distribution & Analytics                            │
├──────────────────────────────┬──────────────────────────────────┤
│ 💰 Wealth Distribution       │ 💎 Current Gil Distribution      │
│     Over Time                │                                  │
│ [Stacked Bar Chart]          │     [Donut Chart]                │
│                              │                                  │
│ Characters: Growing          │   FC Chests: 45%                 │
│ Retainers: Stable            │   Characters: 38%                │
│ FC Chests: Growing           │   Retainers: 17%                 │
└──────────────────────────────┴──────────────────────────────────┘
│                                                                  │
├──────────────────────────────┬──────────────────────────────────┤
│ 🔧 Repair Kits Distribution  │ ⛽ Ceruleum Tanks Distribution    │
│ [Stacked Area Chart]         │ [Stacked Area Chart]             │
└──────────────────────────────┴──────────────────────────────────┘
│                                                                  │
│ 📊 Resource Summary by Type                                     │
│ Entity Type│Total Gil│Repair Kits│Ceruleum Tanks│FC Credits     │
│ ───────────┼─────────┼───────────┼──────────────┼────────       │
│ Character  │   456M  │   1,234   │     890      │    N/A        │
│ Retainer   │   204M  │    567    │     456      │    N/A        │
│ FC Chest   │   540M  │    544    │     545      │  234,567      │
└──────────────────────────────────────────────────────────────────┘
```

**Features**:
- **Stacked visualizations** showing composition over time
- **Pie/donut charts** for current state
- **Comprehensive summary table** with totals
- **Trend identification** (growing, stable, declining)

---

## Color Scheme

The dashboard uses a **dark theme** with carefully chosen colors:

- **Background**: Dark gray (#1F1F1F)
- **Panels**: Slightly lighter gray (#2A2A2A)
- **Text**: White/Light gray
- **Thresholds**:
  - 🔴 Red: Critical/Low values
  - 🟡 Yellow: Warning/Medium values
  - 🟢 Green: Good/High values
  - 🔵 Blue: Excellent/Very high values

## Interactive Features

1. **Click-through filtering**: Click on any legend item to filter
2. **Hover tooltips**: Detailed information on hover
3. **Zoom**: Click and drag to zoom into time series
4. **Cross-panel highlighting**: Selecting one panel highlights related data
5. **Export**: Download data as CSV from any table

## Responsive Design

The dashboard adapts to different screen sizes:
- **Large monitors (>1920px)**: Full 24-column grid layout
- **Standard (1920x1080)**: Optimized layout with all panels visible
- **Laptop (1366x768)**: Scrollable with maintained readability
- **Mobile**: Not recommended, but functional with vertical scrolling

## Performance Indicators

Each panel includes loading indicators:
- **Spinner**: While loading data
- **Error messages**: If query fails
- **No data message**: If time range has no data
- **Last updated**: Timestamp of most recent data point

---

For the actual visual appearance, import the dashboard into your Grafana instance. The JSON configuration ensures consistent rendering across all Grafana installations.
