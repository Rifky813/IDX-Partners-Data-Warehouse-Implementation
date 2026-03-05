# Banking Data Warehouse and ETL Pipeline

**Project-Based Virtual Internship: Data Engineer at ID/X Partners x Rakamin Academy**

## Project Overview
One of ID/X Partners' clients in the banking industry faced challenges in data reporting and analysis. This issue stemmed from transactional and customer data being fragmented across multiple sources, including Excel, CSV files, and Relational Databases. 

This project aims to design a centralized Data Warehouse (DWH) and build an automated ETL (Extract, Transform, Load) pipeline to clean, integrate, and load data, making it readily available for daily analysis and reporting.

<img width="1154" height="484" alt="Screenshot 2026-03-06 000342" src="https://github.com/user-attachments/assets/4faa16ca-1416-43c6-a7d1-f2fdf4833c2e" />

![IDX-Dashboard-PowerBI](https://github.com/user-attachments/assets/1fff4461-fc20-4f6f-acb8-7a7bf6e5ebb0)

## Tech Stack
* **Database Management System:** Microsoft SQL Server (SSMS)
* **ETL Tool:** Talend Open Studio for Data Integration
* **Business Intelligence:** Microsoft Power BI

## Data Architecture and Schema

<img width="725" height="460" alt="Screenshot 2026-03-05 111813" src="https://github.com/user-attachments/assets/7c5d91a0-8d4e-4146-adcc-d5562da59935" />

This project implements a **Star Schema** data modeling approach to denormalize tables for optimized Data Warehouse, consisting of:

**1. Dimension Tables:**
* `DimCustomer`: Stores customer profile data (consolidated from `customer`, `city`, and `state` tables).
* `DimAccount`: Stores customer bank account details.
* `DimBranch`: Stores bank branch details.

**2. Fact Table:**
* `FactTransaction`: Stores historical financial transactions linked to dimension tables via Foreign Keys (`AccountID` and `BranchID`).

*(All tables and columns in the DWH have been standardized using the `PascalCase` format in accordance with business rules).*

## ETL Pipeline Workflow (Talend)

<img width="917" height="374" alt="Screenshot 2026-03-05 060320" src="https://github.com/user-attachments/assets/248dd598-5401-4876-afe4-2a21bc817c9d" />

The ETL process is designed to handle various data source formats and automate data cleansing:

1. **Extract:** Retrieving fragmented transactional data from `transaction_excel` (Excel), `transaction_csv` (CSV), and `transaction_db` (SQL Server).
   * Extracting master data (`account`, `customer`, `branch`, `city`, `state`) from SQL Server.
2. **Transform:**
   * **Unification (`tUnite`):** Merging the three transaction data sources into a single, unified data stream.
   * **Deduplication (`tUniqRow`):** Filtering and removing duplicate transaction rows based on `TransactionID` to maintain data integrity.
   * **Data Enrichment (`tMap`):** Performing an Inner Join to combine customer data with city and state information.
   * **Data Cleansing & Formatting:** Converting string formats to uppercase (excluding ID, Age, and Email) using the `StringHandling.UPCASE()` function in Java.
3. **Load (`tDBOutput`):** * Loading the cleaned data into the DWH tables in SQL Server. The load sequence is orchestrated using a **Master Job** (utilizing the *Trigger On Subjob Ok* connection) to ensure Dimension tables are populated before the Fact table, preventing Foreign Key Constraint Errors.

## Analytical Reporting (Stored Procedures)
To support the business team in generating instant insights, two Stored Procedures were created in SQL Server:

1. **`DailyTransaction`**
   * **Function:** Returns a summary of the total number of transactions (`TotalTransactions`) and the daily total amount (`TotalAmount`).
   * **Parameters:** `@start_date` and `@end_date` to filter the report by a specific date range.
   
2. **`BalancePerCustomer`**
   * **Function:** Dynamically calculates the current balance (`CurrentBalance`) of customers based on their transaction history (Deposit vs. Non-Deposit).
   * **Parameters:** `@name` to search for a specific customer.
   * **Business Rule:** Only processes accounts with an *'active'* status.

## Data Visualization (Power BI Dashboard)
As an added value to this project, the data loaded into the Data Warehouse is visualized using **Microsoft Power BI** to provide actionable insights for business stakeholders.

The interactive dashboard is divided into two main views:
1. **Branch Performance:** Displays transaction trends and daily transaction amounts, while comparing performance across bank branches (including a breakdown of transaction types such as Deposit, Withdrawal, Transfer, and Payment).
2. **Customer Profiling:** Analyzes customer demographics based on gender, account status percentage (active vs. terminated), and geospatial mapping to visualize transaction distribution across various cities.

## Repository Structure
* `sql_scripts/`: DDL scripts for Data Warehouse schema creation and Stored Procedure scripts.
* `assets/`: Images for Documentation.

## Acknowledgments
This project was completed as part of the **Data Engineer Project-Based Virtual Internship** hosted by **ID/X Partners** in collaboration with **Rakamin Academy**.
