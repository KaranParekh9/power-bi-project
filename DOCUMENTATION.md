# Technical Documentation - Global Security Intelligence Dashboard

## Table of Contents
1. [Data Architecture](#data-architecture)
2. [Data Model Design](#data-model-design)
3. [Custom DAX Measures](#custom-dax-measures)
4. [Visualization Specifications](#visualization-specifications)
5. [Performance Optimization](#performance-optimization)
6. [Data Refresh Procedures](#data-refresh-procedures)

## Data Architecture

### Source Systems
The dashboard integrates data from four primary sources:

| Source | Format | Update Frequency | Key Fields |
|--------|--------|------------------|------------|
| OurWorldData Military Database | CSV | Annual | Entity, Year, Military_Expenditure_USD |
| OurWorldData HDI Database | CSV | Annual | Entity, Year, Human_Development_Index |
| OurWorldData Conflict Data | CSV | Annual | Entity, Year, Deaths_in_ongoing_conflicts |
| World Bank Population | CSV | Annual | Entity, Year, Population |

### Data Flow
```
Raw Data Sources → Power Query → Data Model → Visualizations → Dashboard
```

## Data Model Design

### Schema Structure
The dashboard uses a **Star Schema** design optimized for analytical queries:

#### Fact Tables
- `ConflictDeaths` - Battle deaths and conflict metrics
- `military-spending-sipri` - Military expenditure data
- `HDI` - Human development indicators
- `Population` - Demographic data

#### Dimension Tables  
- `Entity` - Country/region lookup
- `Year` - Time dimension
- `Region` - Geographic groupings

### Relationships
```
Entity (1) ←→ (Many) ConflictDeaths
Entity (1) ←→ (Many) military-spending-sipri  
Entity (1) ←→ (Many) HDI
Entity (1) ←→ (Many) Population

Year (1) ←→ (Many) ConflictDeaths
Year (1) ←→ (Many) military-spending-sipri
Year (1) ←→ (Many) HDI  
Year (1) ←→ (Many) Population
```

## Custom DAX Measures

### 1. Military Spending per Capita
**Purpose**: Normalizes military spending by population size for fair country comparison

```dax
Military_Spending_per_Capita = 
DIVIDE(
    SUM('military-spending-sipri'[MilitarySpending_Billion]) * 1000000000,
    SUM('Population'[all years]),
    0
)
```

### 2. Battle Deaths per 100k Population
**Purpose**: Standardizes conflict impact measurement across different population sizes

```dax
Deaths_per_100k = 
DIVIDE(
    SUM('ConflictDeaths'[Deaths in ongoing conflicts]),
    SUM('Population'[all years]),
    0
) * 100000
```

### 3. Military Spending Rank (Dynamic)
**Purpose**: Enables Top N filtering by military expenditure for any selected time period

```dax
Rank_MilitarySpending = 
RANKX(
    FILTER(
        ALL('military-spending-sipri'),
        'military-spending-sipri'[Year] = SELECTEDVALUE('military-spending-sipri'[Year])
    ),
    CALCULATE(SUM('military-spending-sipri'[MilitarySpending_Billion])),
    ,
    DESC,
    Skip
)
```

### 4. Cumulative Death Percentage (Pareto Analysis)
**Purpose**: Calculates running total percentage for 80/20 analysis

```dax
Cumulative_Deaths_Pct = 
VAR RankedEntities = 
    ADDCOLUMNS(
        SUMMARIZE(
            ALLSELECTED('ConflictDeaths'[Entity]),
            'ConflictDeaths'[Entity]
        ),
        "DeathsValue", CALCULATE(SUM('ConflictDeaths'[Deaths in ongoing conflicts])),
        "DeathsRank", RANKX(
            ALLSELECTED('ConflictDeaths'[Entity]),
            CALCULATE(SUM('ConflictDeaths'[Deaths in ongoing conflicts])),
            ,
            DESC,
            Skip
        )
    )
VAR CurrentRank =
    MAXX(
        FILTER(
            RankedEntities,
            'ConflictDeaths'[Entity] = MAX('ConflictDeaths'[Entity])
        ),
        [DeathsRank]
    )
RETURN
DIVIDE(
    SUMX(
        FILTER(
            RankedEntities,
            [DeathsRank] <= CurrentRank
        ),
        [DeathsValue]
    ),
    CALCULATE(
        SUM('ConflictDeaths'[Deaths in ongoing conflicts]), 
        ALLSELECTED('ConflictDeaths')
    )
)
```

### 5. HDI Category Classification
**Purpose**: Groups countries into development tiers for categorical analysis

```dax
HDI_Category = 
SWITCH(
    TRUE(),
    AVERAGE('HDI'[Human Development Index]) >= 0.800, "Very High",
    AVERAGE('HDI'[Human Development Index]) >= 0.700, "High", 
    AVERAGE('HDI'[Human Development Index]) >= 0.550, "Medium",
    "Low"
)
```

## Visualization Specifications

### 1. Global KPI Cards
- **Data Source**: Aggregated measures across all tables
- **Refresh**: Dynamic based on slicer selections
- **Format**: Currency (USD), decimal (2 places), percentage where applicable

### 2. World Map (Choropleth)
- **Visual Type**: ArcGIS Maps or Filled Map
- **Location Field**: Entity (Country)
- **Values**: Battle Deaths per 100k OR Military Spending per Capita
- **Tooltips**: Entity, Year, HDI, Military Spending, Population
- **Color Scheme**: Sequential (light to dark) for intensity

### 3. Time Series Analysis
- **Visual Type**: Line Chart
- **X-Axis**: Year (continuous)
- **Y-Axis**: Military Expenditure % GDP or absolute values
- **Legend**: Entity (filtered to Top 5-10 countries)
- **Interaction**: Cross-filters other visuals

### 4. HDI vs Military Spending Scatter
- **Visual Type**: Scatter Chart
- **X-Axis**: GDP per Capita (log scale recommended)
- **Y-Axis**: Human Development Index (0-1 scale)
- **Size**: Population
- **Color**: Entity or Region
- **Play Axis**: Year (for animation)

### 5. Top Military Spenders Bar Chart (Racing)
- **Visual Type**: Clustered Bar Chart
- **Y-Axis**: Entity (Country)
- **X-Axis**: Military Spending (Billions USD)
- **Small Multiples**: Year (for multi-year comparison)
- **Sort**: Descending by expenditure

## Performance Optimization

### Query Optimization
- **Relationships**: Single direction where possible to reduce processing overhead
- **Calculated Columns**: Minimized in favor of measures for better compression
- **Data Types**: Optimized (integers for years, currency for spending)

### Memory Management
- **DirectQuery**: Not used - all data imported for better performance
- **Aggregations**: Pre-calculated at source where possible
- **Incremental Refresh**: Configured for large historical datasets

### Visual Performance
- **Limit Visuals**: Maximum 5-7 visuals per page
- **Top N Filters**: Applied to reduce data volume in charts
- **Interaction Settings**: Optimized to prevent unnecessary cross-filtering

## Data Refresh Procedures

### Manual Refresh
1. Open Power BI Desktop
2. Click "Refresh" in Home ribbon
3. Verify data connections are active
4. Check for any data quality issues
5. Save and publish to Power BI Service (if applicable)

### Automated Refresh (Power BI Service)
1. Upload .pbix file to workspace
2. Configure data gateway if using on-premises sources
3. Set refresh schedule (daily/weekly)
4. Configure failure notifications
5. Monitor refresh history

### Data Quality Checks
- **Completeness**: Verify no missing years for key countries
- **Accuracy**: Cross-reference totals with original source reports
- **Consistency**: Check for entity name matching across tables
- **Timeliness**: Ensure latest available year data is included

### Troubleshooting Common Issues

#### Connection Errors
- Verify file paths in Power Query
- Check data gateway connectivity (if applicable)
- Update credentials in data source settings

#### Performance Issues
- Reduce visual complexity
- Apply additional filters to limit data volume
- Check for unnecessary bidirectional relationships

#### Data Inconsistencies
- Review entity name standardization
- Verify year alignment across datasets
- Check for null value handling in measures

---

**Technical Lead**: Karan Parekh  
**Last Updated**: August 2025  
**Power BI Version**: Desktop 2024  
**Documentation Version**: 1.0