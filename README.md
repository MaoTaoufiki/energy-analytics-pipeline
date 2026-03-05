# Energy Analytics Pipeline

An end-to-end data engineering project covering the full analytics stack from raw Excel data to an interactive Power BI dashboard built using SSIS, SQL Server, and SSAS.

---

## Architecture Overview

![Architecture](images/architecture.png)

---

## Tech Stack

| Layer | Technology |
|---|---|
| ETL / Orchestration | SQL Server Integration Services (SSIS) |
| Data Warehouse | SQL Server |
| OLAP Cube | SQL Server Analysis Services (SSAS) |
| Visualization | Power BI (Live Connection) |
| Source Data | Excel (.xlsx) |

---

## Data Model Star Schema

**Fact Table:** `Fact_Energy`
- `FactKey`, `CountryKey`, `YearKey`, `EconomyKey`
- `PrimaryEnergyConsumption`, `EnergyPerCapita`, `GreenhouseGasEmissions`
- `RenewablesShareEnergy`, `FossilShareEnergy`, `LowCarbonShareEnergy`

**Dimension Tables:**

| Table | Columns |
|---|---|
| `Dim_Country` | CountryKey, Country, ISOCode |
| `Dim_Year` | YearKey, Year |
| `Dim_Economy` | EconomyKey, CountryKey, YearKey, Population, GDP |

![Star Schema](images/star_schema.png)

---

## SSIS Pipeline

### Control Flow

The package follows a dependency-aware execution order dimensions are loaded before the fact table to ensure foreign key resolution.

![SSIS Control Flow](images/ssis_control_flow.png)

### Data Flow Dimensions

Each dimension uses: `OLE DB Source → Data Conversion → Sort → OLE DB Destination`

![SSIS Dim_Year Data Flow](images/ssis_dim_year.png)

### Data Flow Fact_Energy

Foreign keys are resolved at load time via lookup transforms before inserting into the fact table.

`OLE DB Source → Dim_Country Lookup → Dim_Year Lookup → Dim_Economy Lookup → OLE DB Destination`

![SSIS Fact_Energy Data Flow](images/ssis_fact_energy.png)

---

## SSAS Cube

- Built on top of the SQL Server star schema via SSAS Multidimensional
- Measures: `Greenhouse Gas Emissions`, `Energy Per Capita`, `Primary Energy Consumption`, `Renewables Share Energy`, `Fossil Share Energy`, `Low Carbon Share Energy`
- Calculated measure: `Decarbonization_Rate`
- Dimensions: Country, Year, Economy
- Power BI connects via Live Connection to the SSAS cube

---

## Power BI Dashboard

Single-page dashboard filtered by country slicer, featuring:

- **KPI Card** Energy Per Capita
- **Bar Chart** Decarbonization Rate by Year (positive/negative deviation)
- **Line Chart** Energy Share Breakdown over Time (Fossil vs Low Carbon vs Renewables)
- **Bar Chart** Greenhouse Gas Emissions by Year

![Power BI Dashboard](images/dashboard.png)

---

## Dataset

Energy statistics by country and year, including consumption, emissions, and energy mix breakdowns sourced from publicly available global energy datasets.
