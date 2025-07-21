# 📁 Daily Truncate Load Pipeline – Data Lake File Ingestion

This repository describes a data ingestion pipeline for processing three types of CSV files from a Data Lake container and loading them into respective SQL tables on a **daily truncate and load** basis.

---

## 📦 Supported File Types

| File Type Prefix         | File Pattern                        | Target Table        |
|--------------------------|-------------------------------------|---------------------|
| `CUST_MSTR`              | `CUST_MSTR_YYYYMMDD.csv`            | `CUST_MSTR`         |
| `master_child_export`    | `master_child_export-YYYYMMDD.csv`  | `master_child`      |
| `H_ECOM_ORDER`           | `H_ECOM_ORDER.csv`                  | `H_ECOM_Orders`     |

> Multiple files may exist for different dates. All files are stored in the Data Lake container.

---

## 🚀 Objective

- Ingest and process three categories of files daily.
- Apply specific transformations based on file type.
- Load the data into corresponding SQL tables using truncate-load logic.

---

## 🔁 Processing Logic

### 1. **CUST_MSTR Files**

- Extract the date from the filename (`YYYYMMDD`) and convert it to `YYYY-MM-DD`.
- Add this as a new column `Date`.
- Load the transformed data into the `CUST_MSTR` table.

---

### 2. **master_child_export Files**

- Extract the date from the filename (`YYYYMMDD`).
- Add two new columns:
  - `Date` in `YYYY-MM-DD` format.
  - `DateKey` in `YYYYMMDD` format.
- Load the transformed data into the `master_child` table.

---

### 3. **H_ECOM_ORDER Files**

- No transformation required.
- Load the file as-is into the `H_ECOM_Orders` table.

---

## 🧹 Load Strategy

This process follows a **truncate and load** approach:

1. Clear existing records in the target table.
2. Load newly processed data each day.

---

## 📁 Folder Organization (Data Lake)







> Each folder contains only files relevant to that type. Multiple dated versions of files may exist in the `CUST_MSTR` and `master_child_export` folders.

---

## ⚙️ Processing Logic by File Type

### 1. **CUST_MSTR Files**

- **Pattern:** `CUST_MSTR_YYYYMMDD.csv`
- **Source Folder:** `/CUST_MSTR/`
- **Target Table:** `CUST_MSTR`
- **Transformations:**
  - Extract the date (`YYYYMMDD`) from the filename.
  - Convert it to `YYYY-MM-DD` format.
  - Add a new column named `Date` with this value to each row.
- **Load Type:** Truncate the `CUST_MSTR` table and insert the processed data.

---

### 2. **master_child_export Files**

- **Pattern:** `master_child_export-YYYYMMDD.csv`
- **Source Folder:** `/master_child_export/`
- **Target Table:** `master_child`
- **Transformations:**
  - Extract the date (`YYYYMMDD`) from the filename.
  - Add two columns:
    - `Date`: in `YYYY-MM-DD` format
    - `DateKey`: in `YYYYMMDD` format (as integer or string)
- **Load Type:** Truncate the `master_child` table and insert the processed data.

---

### 3. **H_ECOM_ORDER Files**

- **Pattern:** `H_ECOM_ORDER.csv`
- **Source Folder:** `/H_ECOM_ORDER/`
- **Target Table:** `H_ECOM_Orders`
- **Transformations:**
  - None. The file is loaded **as-is**.
- **Load Type:** Truncate the `H_ECOM_Orders` table and insert the raw data.

---

## 🔁 Truncate and Load Strategy

Each file type uses the **truncate-load** approach:

1. **Truncate** the respective SQL table before ingestion begins.
2. **Insert** the freshly processed or raw data from the current file(s).
3. Ensures clean daily refresh and avoids duplication or overlap.

> This method assumes that each day's files contain the full dataset to be stored in the table.

---

## 🧠 Assumptions

- File names strictly follow the defined naming conventions.
- All files are properly formatted CSVs with headers.
- The pipeline runs on a scheduled basis (e.g., daily).
- All required folders exist in the Data Lake.
- Duplicate file handling, if needed, should be implemented outside or prior to this process.

---

## ✅ Output Summary

| File Prefix          | Target Table      | Required Transformations            |
|----------------------|-------------------|-------------------------------------|
| `CUST_MSTR`          | `CUST_MSTR`       | Add `Date` column (YYYY-MM-DD)      |
| `master_child_export`| `master_child`    | Add `Date` (YYYY-MM-DD), `DateKey` (YYYYMMDD) |
| `H_ECOM_ORDER`       | `H_ECOM_Orders`   | None                                |

---

## 🛠️ Technologies

The pipeline can be implemented using any of the following tools:

- **Azure Data Factory (ADF):** For orchestrating copy activity, data flows, and parameterized pipelines.
- **Azure Data Lake Storage Gen2:** As the source for raw data.
- **Azure SQL Database / SQL Server:** As the destination for processed data.
- **Python / PySpark (Optional):** If additional parsing or preprocessing is required before ADF ingestion.

---

## 📅 Date Extraction Logic

| Filename Pattern                     | Extracted Values                   |
|--------------------------------------|------------------------------------|
| `CUST_MSTR_YYYYMMDD.csv`             | `Date` = `YYYY-MM-DD`              |
| `master_child_export-YYYYMMDD.csv`   | `Date` = `YYYY-MM-DD`, `DateKey` = `YYYYMMDD` |

Date values must be extracted from filenames using string manipulation or regex logic, depending on the pipeline's design.

---

## 📌 Key Recommendations

- Enable logging for each file processed.
- Validate schema of each file before loading.
- Handle unexpected file formats gracefully (e.g., skip or log).
- Use pipeline parameters to make ingestion logic dynamic and reusable.

---

## 📬 Contact

For questions, suggestions, or contributions, feel free to open an issue or submit a pull request.

---








