# 🛒 Olist-Ecommerce-Sales-Analysis

> An end-to-end data engineering and business intelligence pipeline on the Olist Brazilian E-Commerce public dataset — built with PostgreSQL, complex SQL analytics, and Tableau.

[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-336791?logo=postgresql)](https://www.postgresql.org)
[![Tableau](https://img.shields.io/badge/BI-Tableau-E97627?logo=tableau)](https://www.tableau.com)
[![SQL](https://img.shields.io/badge/Language-SQL-blue)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

---

## 📌 Overview

This project demonstrates a production-style data pipeline that ingests raw Olist e-commerce data, models it in PostgreSQL using a snowflake schema, engineers analytics-ready views with complex SQL, and surfaces insights via an executive-grade Tableau dashboard.

**Key Insights Uncovered:**
- 💰 **$15.8M** Total Revenue across 98K+ orders
- 📦 **89% on-time delivery rate** — but the top-grossing category (Health & Beauty) underperforms at **~8.8% late delivery rate**
- 📈 Peak revenue month driven by Q4 holiday marketing: **$1.2M single month**

---

## 🏗️ Architecture & Pipeline

```
┌─────────────────────────────────────────────────────────┐
│                  Raw Data (Olist CSV files)              │
│  customers | orders | order_items | products | payments  │
│  sellers | geolocation | reviews | category_translation  │
└───────────────────────┬─────────────────────────────────┘
                        │  import.sql
                        ▼
┌─────────────────────────────────────────────────────────┐
│              PostgreSQL (Local / Docker)                 │
│  schema.sql → DDL: tables, PKs, FKs, indexes            │
│  import.sql → COPY from CSV into raw tables             │
└───────────────────────┬─────────────────────────────────┘
                        │  Analytical Views
                        ▼
┌─────────────────────────────────────────────────────────┐
│           SQL Analytics Layer (sql/)                     │
│  vw_ecommerce_sales     → master flattened view          │
│  business_queries.sql   → KPI and funnel aggregations    │
│  exploratory_analysis.sql → ad-hoc discovery queries     │
└───────────────────────┬─────────────────────────────────┘
                        │  JDBC bridge
                        ▼
┌─────────────────────────────────────────────────────────┐
│              Tableau Desktop (.twbx)                     │
│  Executive Dashboard:                                    │
│  • Revenue KPIs     • Time-series trends                 │
│  • Delivery status  • Category breakdown                 │
└─────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Database** | PostgreSQL |
| **SQL** | DDL, DML, complex JOINs, analytical views |
| **BI / Visualization** | Tableau Desktop |
| **Connectivity** | JDBC (OpenJDK via Homebrew) |
| **OS / Tooling** | macOS (zsh), Homebrew |

---

## 📁 Repository Structure

```
Olist-Ecommerce-Analytics-Pipeline/
├── sql/
│   ├── schema.sql              # DDL: table definitions, PKs, FKs, indexes
│   ├── import.sql              # COPY statements to load raw CSVs into PostgreSQL
│   ├── business_queries.sql    # KPI aggregations, funnel analysis, top-N queries
│   └── exploratory_analysis.sql # Ad-hoc discovery SQL
├── tableau/
│   └── Ecommerce Sales.twbx    # Packaged Tableau workbook (includes extracted data)
├── images/
│   └── dashboard_preview.png  # Dashboard screenshot
└── README.md
```

---

## 🚀 Setup & How to Run Locally

### Prerequisites
- PostgreSQL 14+
- Java Runtime (OpenJDK 11+ via Homebrew on macOS: `brew install openjdk`)
- Tableau Desktop or [Tableau Reader](https://www.tableau.com/products/reader) (free)
- Olist dataset CSV files from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

### Database Setup

```bash
# 1. Create the database
createdb olist_db

# 2. Connect and run DDL
psql olist_db -f sql/schema.sql

# 3. Place the Olist CSV files in a known path and update import.sql accordingly
# 4. Load data
psql olist_db -f sql/import.sql
```

### Running Analytics Queries

```bash
# KPI queries (revenue, AOV, delivery rates)
psql olist_db -f sql/business_queries.sql

# Exploratory queries
psql olist_db -f sql/exploratory_analysis.sql
```

### Opening the Dashboard

**Option A — Tableau Reader (no licence needed):**
1. Install [Tableau Reader](https://www.tableau.com/products/reader) (free)
2. Open `tableau/Ecommerce Sales.twbx` to interact with the full dashboard

**Option B — Live Tableau Desktop connection via JDBC:**
1. Install OpenJDK: `brew install openjdk`
2. Configure Tableau to use JDBC with connection string:
   `jdbc:postgresql://localhost:5432/olist_db`
3. Connect using your PostgreSQL credentials

> **Note (macOS Gatekeeper):** If Tableau's JDBC `.jar` driver is quarantined, run:
> `xattr -c /path/to/postgresql.jar`

---

## 📊 Dashboard Features

| Panel | Description |
|---|---|
| **Revenue KPIs** | Total Revenue ($15.8M), Orders (98K+), AOV ($141) |
| **Time-Series** | Monthly revenue trend with peak detection |
| **Delivery Status** | On-time vs late, breakdowns per category |
| **Category Deep-Dive** | Top revenue categories vs late delivery rate correlation |
| **Geographic** | Order volume and delivery performance by seller state |

---

## 🔍 Key SQL Engineering Highlights

- **Master view `vw_ecommerce_sales`**: Flattens a 9-table snowflake schema into a single analytics-ready surface via multi-join SQL.
- **Delivery Status column**: Calculated directly in SQL using `EXTRACT(EPOCH FROM ...)` timestamp differentials — no post-processing needed.
- **Category Translation join**: Portuguese→English category mapping joined inline for BI-ready labels.

---

## 📄 License

MIT © [Ommprakash Mohanty](https://github.com/OmmprakashMohanty01)
