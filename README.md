# 📚 BookStore OLTP to Data Warehouse ETL Pipeline

## Project Overview
This project implements a complete End-to-End ETL (Extract, Transform, Load) pipeline using **Python** to migrate data from a transactional "BookStore" SQL database (OLTP) to a structured **Data Warehouse (DWH)** for analytical purposes.

The pipeline automates the data movement, ensuring data cleanliness, correct data typing, and optimal structuring for reporting.

## 🛠️ Technology Stack
* **Language:** Python 3.13
* **Source DB:** SQL Server (using `pyodbc`)
* **Processing Library:** Pandas
* **Destination DWH:** SQL Server (using `pyodbc` with efficient cursor batching)

## 🏗️ ETL Architecture
### Pipeline Flow Diagram

```mermaid
graph TD
    %% Define Nodes with Icons
    A[("🗄️ Source DB<br/>(BookStore_EG)")]
    B{{"💻 Python ETL<br/>(Pandas)"}}
    C[("📊 Data Warehouse<br/>(BookDWH)")]

    %% Transformation Steps (Sub-graph for clarity)
    subgraph Transformations ["🔧 Transformation Stage (Pandas)"]
        direction TB
        T1["🧼 Clean Currency<br/>(e.g., '50$' -> 50)"]
        T2["📅 Fix Date Formats"]
        T3["🚫 Handle Nulls &<br/>Duplicates"]
        T4["📐 Schema Mapping<br/>(Facts & Dimensions)"]
    end

    %% Define Connections
    A -- "📥 Extract (pyodbc)" --> B
    B ==> Transformations
    Transformations ==> T4
    T4 -- "📤 Load (pyodbc)" --> C

    %% Apply Styles
    style A fill:#e1f5fe,stroke:#01579b,stroke-width:2px,color:#01579b
    style B fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,color:#fbc02d
    style C fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#2e7d32
    style Transformations fill:#f5f5f5,stroke:#9e9e9e,stroke-dasharray: 5 5

The pipeline follows a classic 3-step Data Engineering approach:

### 1️⃣ Extraction (`Extract`)
Data is pulled from transactional views (`VCustomer`, `VBook`, `VOrders`) within the Source `BookStore_EG` database. This ensures we are reading consistent, structured snapshots.

* `pyodbc` connects to `localhost`.
* Pandas `read_sql` is used to load data into DataFrames.

### 2️⃣ Transformation (`Transform`)
The most crucial step. Our raw transactional data undergoes several cleaning and restructuring processes:

* **Handling Null Values:** Imputing missing data to maintain analysis integrity.
* **Data Deduplication:** Ensuring zero duplicated records in dimension tables.
* **Data Type Casting:** Fixing incorrect schema types (e.g., converting text-based currency fields like "50$" to integers, formatting dates).
* **Data Standardisation:** Correcting naming conventions for cities (e.g., merging "Cairo" and "Cairo, cairo") and standardising salary/age outliers.
* **Schema Design:** Shaping data into dimension tables (`Dim_Customer`, `Dim_Book`) and a central fact table (`Fact_Order`).

### 3️⃣ Loading (`Load`)
Finally, the cleansed and restructured data is batched-inserted into the Destination Data Warehouse (`BookDWH`).

* A connection is established to the new `BookDWH`.
* `cursor.execute` with batching is used to insert data iteratively into the new star-schema structure efficiently.

## 📈 Impact
* **Operational Stability:** Offloads reporting query load from the transactional database.
* **Data Quality:** Improves data cleanliness and consistency by [X%] (add an estimated percentage if available, otherwise just mention improved data quality).
* **Reporting Readiness:** Prepares data for seamless integration with BI tools like Power BI or Tableau.

## 🚀 Future Improvements
* Implement incremental loading (currently full reload).
* Add error logging and email notifications for pipeline failures.
* Containerize the entire pipeline using Docker.

---
**Disclaimer:** This project was developed as a comprehensive exercise in data engineering principles.
