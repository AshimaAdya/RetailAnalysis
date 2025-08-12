# Retail Medallion Architecture with Delta Lake (Databricks)

## 📌 Overview
This project implements a **Medallion Architecture** (Bronze → Silver → Gold) using **PySpark** and **Delta Lake** on Databricks.  
It ingests raw retail CSV data, cleans and enriches it, and produces optimized analytical tables for reporting.

---

## 🏗 Architecture
```
Raw CSVs
   ↓
Bronze Layer  → Raw data stored in Delta format.
   ↓
Silver Layer  → Cleaned, validated, enriched data.
   ↓
Gold Layer    → Star schema, fact & dimension tables, aggregated metrics.
```

- **Bronze:** Exact copy of source data, minimal transformations.
- **Silver:** Data cleaning, deduplication, type casting, null handling, derived columns.
- **Gold:** Star schema modeling, fact/dimension tables, aggregations, and Delta Lake optimizations.

---

## 📂 Project Structure
```
.
├── raw_bronze.py        # Ingests CSV files → Bronze Delta tables
├── transform_silver.py  # Cleans & enriches Bronze → Silver tables
├── gold_modelling.py    # Creates Gold fact/dimension tables, aggregations, optimizes storage
└── README.md
```

---

## 📊 Data Sources
The following CSV files are expected under `/Volumes/workspace/retail/retail/`:
- `customers.csv`
- `products.csv`
- `promotions.csv`
- `pos_sales.csv`
- `stores.csv`

---

## ⚙️ Requirements
- **Databricks Runtime** with Delta Lake support.
- Python 3.x
- PySpark
- Delta Lake (`delta-spark` library)

---

## 🚀 Execution Steps

### 1️⃣ Bronze Layer – Raw Ingestion
Reads raw CSVs and stores them as Delta tables in the `bronze` database.
```bash
python raw_bronze.py
```
Creates:
- `bronze.customers`
- `bronze.products`
- `bronze.promotions`
- `bronze.pos_sales`
- `bronze.stores`

---

### 2️⃣ Silver Layer – Transform & Clean
Cleans nulls, validates values, casts data types, and adds derived columns.
```bash
python transform_silver.py
```
Creates:
- `silver.customers`
- `silver.products`
- `silver.promotions`
- `silver.pos_sales`
- `silver.stores`

Optimizes `silver.pos_sales` via **compaction**.

---

### 3️⃣ Gold Layer – Modeling & Aggregations
Builds a **Star Schema** with fact and dimension tables.  
Adds an aggregated view of sales by category and loyalty status.
```bash
python gold_modelling.py
```
Creates:
- `gold_retail.fact_sales` (Z-Ordered by `customer_id`)
- `gold_retail.agg_sales_by_category_loyalty` (compacted)
- `gold_retail.dim_customers`
- `gold_retail.dim_products`
- `gold_retail.dim_stores`
- `gold_retail.dim_promotions`
- `gold_retail.dim_date`

---

## 📈 Optimizations
- **Compaction:** Merges small files for faster reads.
- **Z-Ordering:** Clusters data by `customer_id` in `fact_sales` to improve query performance on that column.
- **Partitioning:** Sales tables partitioned by `transaction_date`.

---

## 📌 Notes
- This pipeline is designed for **Databricks** but can be adapted for other Spark + Delta environments.
- Make sure the Databricks cluster has access to the CSV source path.
