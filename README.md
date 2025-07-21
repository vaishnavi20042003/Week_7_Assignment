# 📊 Daily File Ingestion Pipeline – Data Lake to SQL Workflow

This repository defines a structured data ingestion pipeline designed to process daily CSV files from a Data Lake container and load them into dedicated SQL tables following a **truncate-and-load** strategy.

---

## 📂 File Classification

| File Category              | File Name Pattern                          | Destination SQL Table    |
|----------------------------|--------------------------------------------|--------------------------|
| **Customer Master**        | `CUST_MSTR_YYYYMMDD.csv`                  | `CUST_MSTR`              |
| **Master-Child Relationship** | `master_child_export-YYYYMMDD.csv`       | `master_child`           |
| **E-Commerce Orders**      | `H_ECOM_ORDER.csv`                        | `H_ECOM_Orders`          |

✅ Files can have multiple date-stamped versions. All are stored in the Data Lake source container.

---

## 🎯 Purpose of Pipeline

- ✔️ **Automate Daily Data Loads** from Data Lake into SQL tables
- ✔️ **Transform Data** where applicable by enriching with date metadata
- ✔️ **Apply Truncate-Load Strategy** for clean daily refresh
- ✔️ **Ensure Scalability & Reliability** with structured processing

---

## 📝 Detailed File Processing Rules

### 1. 🟦 Customer Master Files
- **Pattern:** `CUST_MSTR_YYYYMMDD.csv`
- **Source Directory:** `/CUST_MSTR/`
- **SQL Table:** `CUST_MSTR`
- **Logic:**
    - Extract date from the filename (`YYYYMMDD`)
    - Convert it to `YYYY-MM-DD` and add as `Date` column
    - **Truncate table before load**

---

### 2. 🟨 Master-Child Relationship Files
- **Pattern:** `master_child_export-YYYYMMDD.csv`
- **Source Directory:** `/master_child_export/`
- **SQL Table:** `master_child`
- **Logic:**
    - Extract date from the filename (`YYYYMMDD`)
    - Create:
      - `Date` column → `YYYY-MM-DD` format
      - `DateKey` column → `YYYYMMDD` format
    - **Truncate table before load**

---

### 3. 🟩 E-Commerce Orders Files
- **Pattern:** `H_ECOM_ORDER.csv`
- **Source Directory:** `/H_ECOM_ORDER/`
- **SQL Table:** `H_ECOM_Orders`
- **Logic:**
    - No transformation
    - Load data **as-is**
    - **Truncate table before load**

---

## 🔄 Daily Truncate and Reload Methodology

- All target tables are **fully refreshed daily**:
    - ✅ **Step 1:** Truncate table
    - ✅ **Step 2:** Load fresh data
- This ensures:
    - No data duplication
    - Clean historical snapshots based on daily loads

---

## 📌 Operational Assumptions

- Files strictly follow naming conventions and reside in proper directories
- All CSV files have headers and valid schemas
- The pipeline executes daily on a scheduled trigger
- Duplicate file handling is managed externally (outside this script)

---

## 🧾 Final Output Summary

| File Category              | SQL Table           | Transformations Applied                     |
|----------------------------|---------------------|---------------------------------------------|
| **CUST_MSTR**              | `CUST_MSTR`         | Adds `Date` column (YYYY-MM-DD)             |
| **master_child_export**    | `master_child`      | Adds `Date` (YYYY-MM-DD) and `DateKey`      |
| **H_ECOM_ORDER**           | `H_ECOM_Orders`     | No transformation; direct load              |

---

## 🛠️ Tools & Technologies Used

- **Azure Data Lake Gen2**: Raw data storage layer
- **Azure SQL Database**: Destination for processed data
- **Apache Spark / PySpark**: Data transformation and ingestion engine
- **(Optional)** **Azure Data Factory**: Orchestration (if needed)

---

## 🗓️ Date Extraction Logic Reference

| File Name Pattern                      | Extracted Fields                          |
|-----------------------------------------|-------------------------------------------|
| `CUST_MSTR_YYYYMMDD.csv`                | `Date = YYYY-MM-DD`                       |
| `master_child_export-YYYYMMDD.csv`      | `Date = YYYY-MM-DD`, `DateKey = YYYYMMDD` |
| `H_ECOM_ORDER.csv`                      | No date extraction                        |

---

## 💡 Best Practices Followed

- ✅ **Logging Enabled** for each processing step
- ✅ **Schema Validation** before loading to SQL
- ✅ **Graceful Handling of Unexpected Files**
- ✅ **Reusable Structure** for future scalability

---

## 🚀 Quick Start

```bash
spark-submit scripts/daily_load.py
