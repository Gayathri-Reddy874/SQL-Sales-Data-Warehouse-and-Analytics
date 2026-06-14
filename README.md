# SQL Sales Data Warehouse and Analytics

An end-to-end SQL portfolio project covering the full data lifecycle — from raw, multi-source data ingestion to a clean, business-ready data warehouse, through to an advanced SQL analytics and reporting layer built on top of it.

The project is structured in two connected parts that together tell one complete story: **build the warehouse, then use it.**

```
SQL-Sales-Data-Warehouse-and-Analytics/
├── data-warehouse/       # Part 1: ETL pipeline + Medallion Architecture (Bronze → Silver → Gold)
└── data-analytics/       # Part 2: Exploratory & business analytics on the Gold layer
```

---

**Author:** Mallareddygari Gayathri | [GitHub](https://github.com/Gayathri-Reddy874)

---

## Table of Contents

- [Project Overview](#project-overview)
- [Part 1 — Data Warehouse](#part-1--data-warehouse)
- [Part 2 — Data Analytics](#part-2--data-analytics)
- [Tech Stack](#tech-stack)
- [How to Run](#how-to-run)
- [Skills Demonstrated](#skills-demonstrated)

---

## Project Overview

**Objective:** Consolidate raw sales data from two source systems (CRM and ERP) into a single, analytics-ready data warehouse using SQL Server, then apply a structured layer of SQL-based analytics to extract actionable business insights on customer behavior, product performance, and sales trends.

**Domain:** Sales Analytics | **Database:** SQL Server (T-SQL) | **Architecture:** Medallion (Bronze / Silver / Gold)

---

## Part 1 — Data Warehouse (`data-warehouse/`)

### Architecture

The warehouse follows **Medallion Architecture** with three progressive layers:

| Layer | Purpose |
|---|---|
| **Bronze** | Raw data loaded as-is from CRM and ERP CSV sources into staging tables — no transformations applied. Uses `BULK INSERT` via stored procedures with batch timing and error handling. |
| **Silver** | Data cleansing, standardization, deduplication, type casting, and normalization to resolve cross-system inconsistencies and prepare data for modeling. |
| **Gold** | Business-ready **star schema** — `dim_customers`, `dim_products`, and `fact_sales` — optimized for analytical queries and reporting. Exposed as SQL views. |

### Data Sources

Two independent source systems integrated into a single unified model:

| Source | Tables |
|---|---|
| **CRM** | `crm_cust_info`, `crm_prd_info`, `crm_sales_details` |
| **ERP** | `erp_cust_az12` (demographics), `erp_loc_a101` (geography), `erp_px_cat_g1v2` (product categories) |

### Repository Structure

```
data-warehouse/
├── datasets/
│   ├── source_crm/          # Raw CRM exports (customers, products, sales)
│   └── source_erp/          # Raw ERP exports (demographics, location, categories)
├── docs/
│   ├── medallion_architecture_flow.png
│   ├── preview.png
│   ├── data_catalog.md      # Field-level documentation for all Gold layer tables
│   └── naming_conventions.md
├── scripts/
│   ├── bronze/              # DDL + stored procedures for raw data ingestion
│   ├── silver/              # DDL + stored procedures for cleaning and transformation
│   └── gold/                # Views for the final star schema
└── tests/                   # Data quality checks (nulls, duplicates, referential integrity)
```

### Gold Layer — Star Schema

**`gold.dim_customers`** — Customer dimension enriched with demographic and geographic data (name, country, gender, marital status, birthdate, create date).

**`gold.dim_products`** — Product dimension with category, subcategory, product line, cost, and maintenance attributes.

**`gold.fact_sales`** — Sales fact table capturing order number, product key, customer key, order/ship/due dates, sales amount, quantity, and price.

---

## Part 2 — Data Analytics (`data-analytics/`)

### Overview

A set of **14 progressively-built SQL scripts** that answer real business questions using the Gold layer, from basic exploration through to reusable reporting views.

### Analytics Scripts

| Script | Analysis Type | Key Techniques |
|---|---|---|
| `00_init_database.sql` | Database setup | Schema initialization |
| `01_database_exploration.sql` | Schema discovery | `INFORMATION_SCHEMA`, metadata queries |
| `02_dimensions_exploration.sql` | Dimension profiling | Distinct values, null checks |
| `03_date_range_exploration.sql` | Temporal boundaries | `MIN()`, `MAX()`, `DATEDIFF()` |
| `04_measures_exploration.sql` | KPI baseline | Aggregations, averages, totals |
| `05_magnitude_analysis.sql` | Cross-dimension comparison | `GROUP BY`, sales by category/country |
| `06_ranking_analysis.sql` | Top/bottom N | `RANK()`, `DENSE_RANK()`, `TOP N` |
| `07_change_over_time_analysis.sql` | Trend analysis | Monthly/yearly sales trends, `DATETRUNC()` |
| `08_cumulative_analysis.sql` | Running totals | `SUM() OVER()`, `AVG() OVER()` — moving averages |
| `09_performance_analysis.sql` | YoY comparison | `LAG()`, above/below average classification |
| `10_data_segmentation.sql` | Customer & product segmentation | `CASE`, VIP / Regular / New customer groups |
| `11_part_to_whole_analysis.sql` | Contribution analysis | Category share of total revenue |
| `12_report_customers.sql` | Customer report view | RFM-style KPIs — recency, AOV, monthly spend, age groups |
| `13_report_products.sql` | Product report view | Revenue segments, performance KPIs |

### Key Analytical Outputs

**Customer Report (`gold.report_customers`)** — A reusable SQL view that segments customers into VIP / Regular / New based on lifespan and total spend, computes age groups, recency (months since last order), average order value (AOV), and average monthly spend.

**Product Report (`gold.report_products`)** — A reusable SQL view capturing product-level revenue segments, order frequency, and performance KPIs for business reporting.

### Repository Structure

```
data-analytics/
├── datasets/
│   └── gold-layer-exports/   # CSV snapshots of dim_customers, dim_products, fact_sales
│                              # (run analytics standalone without rebuilding the warehouse)
└── scripts/                   # 14 SQL scripts (00 → 13)
```

---

## Tech Stack

| Tool | Purpose |
|---|---|
| **SQL Server (T-SQL)** | Data warehouse, ETL stored procedures, analytics queries |
| **SSMS** | Query execution and database management |
| **BULK INSERT** | High-performance CSV ingestion into Bronze layer |
| **Draw.io** | Architecture, data flow, ETL, and data model diagrams |
| **Git / GitHub** | Version control and portfolio publishing |

---

## How to Run

### Prerequisites
- SQL Server Express (free) — [Download here](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- SQL Server Management Studio (SSMS) — [Download here](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)

### Part 1 — Build the Data Warehouse

```sql
-- Step 1: Initialize the database and schemas
EXEC data-warehouse/scripts/init_database.sql

-- Step 2: Load Bronze layer (raw ingestion)
EXEC bronze.load_bronze;

-- Step 3: Load Silver layer (clean & transform)
EXEC silver.load_silver;

-- Step 4: Create Gold layer views (star schema)
-- Run all scripts in scripts/gold/

-- Step 5: Validate data quality
-- Run all scripts in tests/
```

### Part 2 — Run the Analytics Layer

Option A — Run against the Gold layer built in Part 1:
```
Run data-analytics/scripts/00 through 13 in order against your SQL Server instance.
```

Option B — Run standalone (no warehouse required):
```
Load data-analytics/datasets/gold-layer-exports/*.csv into a fresh database,
then run the analytics scripts against those tables.
```

---

## Skills Demonstrated

| Category | Skills |
|---|---|
| **Data Engineering** | ETL pipeline design, stored procedures, `BULK INSERT`, batch processing, error handling |
| **Data Architecture** | Medallion Architecture (Bronze / Silver / Gold), star schema, dimensional modeling |
| **Data Quality** | Null checks, duplicate detection, referential integrity tests, standardization validation |
| **SQL Analytics** | Window functions (`LAG`, `SUM OVER`, `AVG OVER`, `RANK`), CTEs, subqueries, `CASE` logic |
| **Business Analytics** | Customer segmentation, RFM analysis, YoY performance, trend analysis, part-to-whole |
| **Documentation** | Data catalog, naming conventions, architecture diagrams, inline SQL documentation |
