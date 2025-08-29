# Global Security Intelligence Dashboard

[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow)](https://powerbi.microsoft.com/)
[![License: Free](https://img.shields.io/badge/License-Free-blue.svg)](https://github.com/)
[![Status: Active](https://img.shields.io/badge/Status-Active-green)](https://github.com/)

A comprehensive Power BI dashboard designed for UN peace and security analysts to monitor global conflict patterns, military expenditure trends, and development indicators from 1950-2024.

## 📊 Dashboard Overview

This interactive dashboard provides strategic insights into global security dynamics by analyzing relationships between military spending, human development, population dynamics, and conflict outcomes across 150+ countries.

### Key Features
- **Real-time KPI Monitoring**: Global defence outlay, battle deaths per 100k, military spending per capita
- **Geographic Analysis**: Interactive world map highlighting conflict hotspots and military spending concentration
- **Temporal Trends**: Multi-decade analysis of defence burden and conflict patterns
- **Comparative Analytics**: HDI vs military spending correlation analysis
- **Top Performers**: Pareto analysis of military expenditure and conflict impact

## 🎯 Target Users

**Primary User**: UN Peace and Security Analysts
- Monitor global conflict risk and military spending patterns
- Assess regional security threats and resource allocation
- Provide evidence-based recommendations for peacekeeping interventions
- Track correlation between development indicators and security metrics

## 📁 Project Structure

```
├── README.md                          # Project documentation
├── DOCUMENTATION.md                   # Detailed technical documentation
├── data/                             # Data source files
│   ├── military-spending-sipri.csv   # SIPRI military expenditure data
│   ├── hdi-data.csv                  # UNDP Human Development Index
│   ├── conflict-deaths.csv           # Uppsala/PRIO conflict data
│   └── population-data.csv           # UN/World Bank population data
├── dashboard/
│   ├── CA1_20060599-dashboard.pbix   # Main Power BI dashboard file
│   └── CA1_20060599-dashboard.pdf    # Dashboard export/preview
├── presentations/
│   └── CA1_20060599_Presentation.pdf # Project presentation slides
└── docs/
    ├── data-dictionary.md            # Data field definitions
    └── dax-measures.md               # Custom DAX calculations
```

## 🔧 Setup Instructions

### Prerequisites
- Power BI Desktop (latest version)
- Windows 10/11 or Power BI Service access
- Minimum 4GB RAM recommended

### Installation
1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/global-security-dashboard.git
   cd global-security-dashboard
   ```

2. **Open the dashboard**
   - Launch Power BI Desktop
   - Open `dashboard/Global_Defence-dashboard.pbix`
   - Allow data refresh when prompted

3. **Data Sources**
   - Ensure data files in `/data` folder are accessible
   - Refresh data connections if needed
   - Update file paths in Power Query if repository location changes

## 📈 Key Visualizations

### 1. Global KPIs
- **Global Defence Outlay**: $797.07bn (2024)
- **Battle Deaths per 100k**: 3.11 average
- **Military Spending per Capita**: $2.10 average

### 2. Core Analytics
- **Conflict Hotspot Map**: Geographic visualization of battle deaths and military spending
- **Defence Burden Timeline**: Military expenditure trends from 1950-2024
- **Top Military Spenders**: Ranking of countries by absolute spending
- **HDI-Security Correlation**: Bubble chart analyzing development vs military investment

## 🔄 Data Sources

| Source | Dataset | Coverage | Last Updated |
|--------|---------|----------|--------------|
| Our World Data | Military Expenditure | 1950-2024 | Annual |
| Our World Data | Human Development Index | 1990-2024 | Annual |
| Our World Data | Armed Conflict Database | 1946-2024 | Annual |
| World Bank | Population Data | 1960-2024 | Annual |

## 🧮 Custom Measures

### 1. Military Spending per Capita
```dax
Military_Spending_per_Capita = 
DIVIDE(
    SUM('military-spending-sipri'[MilitarySpending_Billion]) * 1000000000,
    SUM('Population'[all years])
)
```

### 2. Battle Deaths per 100k
```dax
Deaths_per_100k = 
DIVIDE(
    SUM('ConflictDeaths'[Deaths in ongoing conflicts]),
    SUM('Population'[all years])
) * 100000
```

## 🚀 Usage Guide

### Interactive Features
- **Year Slicer**: Filter all visuals by specific years or ranges
- **Country Selection**: Click any country to cross-filter all visualizations  
- **Regional Analysis**: Use map selection tools for geographic filtering
- **Drill-down**: Right-click charts for detailed country-level analysis

### Key Insights
- Identify which 20% of countries account for 80% of global military spending
- Analyze correlation between HDI and military expenditure patterns
- Track conflict trend patterns and emerging hotspots
- Compare regional defence spending evolution over decades

## 📝 Technical Specifications

- **Power BI Version**: Desktop (latest)
- **Data Model**: Star schema with fact tables for spending, deaths, HDI, population
- **Refresh Frequency**: Manual (can be automated with Power BI Service)
- **Performance**: Optimized for datasets up to 1M rows per table
- **Cross-filtering**: Bidirectional relationships enabled for interactive analysis

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/enhancement`)
3. Commit your changes (`git commit -am 'Add new analysis'`)
4. Push to the branch (`git push origin feature/enhancement`)
5. Create a Pull Request

### Contribution Guidelines
- Update documentation for any new visualizations
- Test data refresh functionality
- Maintain consistent naming conventions
- Include DAX formula comments for custom measures

## 📄 License

This project is licensed under the Free License.

## 🎓 Academic Context

This dashboard was developed as part of an academic assignment focused on data visualization for international security analysis. It demonstrates practical application of business intelligence tools in peace and security research.

## 📞 Contact

- **Project Author**: Karan Parekh
- **Institution**: Dublin Business School
- **Email**: karanparekh2298@gmail.com
- **LinkedIn**: https://www.linkedin.com/in/karan-parekh-13819b175/

##  Acknowledgments

- Our World Data for comprehensive military expenditure data
- Our World Data for Human Development Index datasets
- Our World Data for conflict statistics
- World Bank for population and economic indicators
- Power BI community for visualization best practices

---

**Last Updated**: August 2025  
**Dashboard Version**: 1.0  
**Data Coverage**: 1950-2024
