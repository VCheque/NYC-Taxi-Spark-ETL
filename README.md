## NYC Taxi Trip Records ETL Pipeline Optimization

This repository contains the complete code and documentation for an end-to-end Data Engineering and Business Intelligence (BI) pipeline developed using **PySpark/Databricks** and the **Delta Lake** format.

The project demonstrates proficiency in big data processing, data quality assurance, advanced analytical transformations (Window Functions), and, crucially, **performance tuning** for large-scale production environments.

### Project Architecture (End-to-End Flow)



The diagram above illustrates the ETL (Extract, Transform, Load) process: raw Parquet files are ingested, cleaned and engineered via PySpark, joined with a lookup table, and finally stored in the performant Delta Lake format, ready for BI consumption (Power BI/Tableau).

###  Technology Stack

| Role | Technology | Purpose in Project |
| :--- | :--- | :--- |
| **Data Lakehouse** | **Databricks (Spark)** | Distributed processing and ETL orchestration. |
| **Cloud Storage** | **Unity Catalog Volumes** | Securely storing raw Parquet and CSV files. |
| **Data Warehouse** | **Delta Lake** | Target storage format with ACID properties for reliable, query-optimized data. |
| **Programming** | **PySpark (Python)** | Data cleaning, feature engineering, and pipeline logic. |
| **Visualization** | **Power BI / Tableau** | Used to connect to the final Delta table for dashboard creation (final deliverable). |


### The Engineering Challenge & Solution

The initial implementation of the ETL pipeline suffered from poor performance, primarily due to **data shuffling** during the join operation between the massive trip data and the small Taxi Zone Lookup Table.

| Metric | Baseline Pipeline (Unoptimized) | Optimized Pipeline (Broadcast Join) | **Result** |
| :--- | :--- | :--- | :--- |
| **Key Optimization** | Sort-Merge Join (High Shuffle) | **Broadcast Hash Join (Zero Shuffle)** | Eliminated Network Overhead |
| **Max Shuffle Write** | *TP* | **0 GB** | **100% Reduction** |
| **Total Pipeline Runtime** | *TP* | *TP* | **TP% Reduction** |

#### **Optimization Rationale: Broadcast Join**

By applying the `pyspark.sql.functions.broadcast()` hint to the small lookup table, Spark was instructed to replicate the entire table across all worker nodes. This technique completely bypasses the expensive "shuffle" phase for the large trip data, resulting in a significantly faster and more resource-efficient join. This demonstrates expertise in **Spark performance tuning** and cost-conscious data engineering.


### Analytical Transformations & Features

The pipeline was built to derive key analytical features essential for business decision-making:

1.  **Data Quality Filtering:** Implemented strict filters to remove corrupt records (e.g., `trip_distance = 0`, `fare_amount < $1`, and unrealistic speeds).
2.  **Derived Features:** Calculated **Trip Duration (seconds)** and **Average Speed (MPH)** to enable congestion and driver efficiency analysis.
3.  **Complex Analysis (Window Function):** Used a **Spark Window Function** partitioned by `Pickup_Zone` and ordered by `date` to calculate the **7-day Rolling Average Fare**. This metric is critical for identifying transient pricing trends and market fluctuations.
4.  **Optimized Load:** Final data was partitioned by `pickup_year` and `pickup_month` into **Delta Lake** to optimize query performance for BI tools.

### Power BI Integration

The results of the ETL process was a 
