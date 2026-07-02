# Olist Azure Data Engineering Project

## Project Overview
End to end data engineering project built on Microsoft Azure
using the Medallion Architecture (Bronze, Silver, Gold layers)
to process the Brazilian E-Commerce dataset from Olist.

The pipeline ingests 9 CSV files containing 1.5 million rows,
transforms them through 3 layers of processing, and produces
8 business-ready gold tables for analytics and reporting.

---

## Architecture
CSV Files (ADLS Gen2)
↓
Azure Data Factory (Orchestration)
↓
Databricks PySpark (Processing)
↓
Bronze Layer → Silver Layer → Gold Layer
↓
Unity Catalog (Delta Lake Tables)

---

## Technologies Used

| Technology | Purpose |
|---|---|
| Azure Data Lake Gen2 | Raw file storage |
| Azure Data Factory | Pipeline orchestration |
| Azure Databricks | PySpark processing |
| Delta Lake | Table format |
| Unity Catalog | Data governance |
| Azure Key Vault | Secret management |
| PySpark | Data transformation |

---

## Dataset

**Source:** Olist Brazilian E-Commerce Dataset

| Table | Rows |
|---|---|
| customers | 99,441 |
| orders | 99,441 |
| order_items | 112,650 |
| order_payments | 103,886 |
| order_reviews | 104,162 |
| products | 32,951 |
| sellers | 3,095 |
| geolocation | 1,000,163 |
| category_translation | 71 |
| **Total** | **1,555,860** |

---

## Project Structure
olist-azure-data-engineering/
│
├── configs/
│   └── config.py              # Central configuration
│
├── bronze/
│   └── bronze_ingestion.py    # Raw data ingestion
│
├── silver/
│   ├── silver_customers.py
│   ├── silver_orders.py
│   ├── silver_order_items.py
│   ├── silver_order_payments.py
│   ├── silver_order_reviews.py
│   ├── silver_products.py
│   ├── silver_sellers.py
│   └── silver_geolocation.py
│
├── gold/
│   ├── gold_sales_summary.py
│   ├── gold_product_performance.py
│   ├── gold_seller_performance.py
│   ├── gold_customer_segments.py
│   ├── gold_delivery_analysis.py
│   ├── gold_payment_analysis.py
│   ├── gold_review_analysis.py
│   └── gold_rfm_segments.py
│
├── utils/
│   └── data_quality_checks.py
│
└── pipeline/
└── master_pipeline.py

---

## Medallion Architecture

### Bronze Layer
- Reads all 9 CSV files from ADLS Gen2
- Adds audit columns (ingestion_timestamp, source_file)
- Writes as Delta tables — no transformations

### Silver Layer
- Deduplication on primary keys
- Data type casting (strings to timestamps)
- Null handling and string standardisation
- Derived columns (delivery_delay_days, order_year etc)
- Fixed CSV corruption in order_reviews table
- Reduced geolocation from 1M to 19K rows using groupBy
- Joined products with English category translations

### Gold Layer
- Monthly revenue with month over month growth
- Product category performance rankings
- Seller performance leaderboard
- Customer segmentation by state and region
- Delivery SLA analysis (on time vs late)
- Payment type and instalment analysis
- Review sentiment by product category
- RFM customer segmentation

---

## Key Business Insights
Total Revenue        : R$ 15,422,461.77
Total Orders         : 96,478 delivered
Top Category         : health_beauty (R$ 1.2M)
On Time Delivery     : 90.4%
Platform Avg Rating  : 4.01 / 5
Credit Card Usage    : 73.9% of payments

### RFM Customer Segments
| Segment | Customers | Avg Spend |
|---|---|---|
| Champions | 15,084 | R$ 285.26 |
| Loyal | 27,645 | R$ 147.22 |
| Potential | 30,932 | R$ 169.74 |
| At Risk | 14,903 | R$ 92.04 |
| Lost | 7,914 | R$ 54.04 |

---

## Data Quality Framework

Reusable functions validating all 25 tables:
- Row count validation
- Null checks on key columns
- Duplicate detection
- Schema validation
- Value range checks

**All checks passing across Bronze, Silver and Gold layers.**

---

## Pipeline Orchestration

Azure Data Factory triggers the master pipeline
daily at 11PM IST automatically.
ADF Trigger (Daily 11PM IST)
↓
PL_Olist_Master_Pipeline
↓
master_pipeline notebook
↓
18 notebooks run in sequence
↓
25 Delta tables refreshed

---

## How to Run

1. Set up Azure resources (ADLS, Databricks, ADF, Key Vault)
2. Create secret scope linking Databricks to Key Vault
3. Upload CSV files to ADLS raw container
4. Run notebooks in order:
   - configs/config
   - bronze/bronze_ingestion
   - silver/* (all 8 notebooks)
   - gold/* (all 8 notebooks)
   - utils/data_quality_checks
5. Or trigger master_pipeline for full run

---

## Author
Akash Srikanth
