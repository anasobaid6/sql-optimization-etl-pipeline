# Upwork Portfolio Listing: Fault-Tolerant ETL Pipeline & SQL Optimization Benchmark

---

## 📌 Project Header

**Project Title:**  
Fault-Tolerant Pandera ETL Pipeline & Empirical SQL Query Optimization Benchmark (500,000 Records)

**Project Category:**  
Data Engineering / Database Architecture & Administration / SQL Query Performance Tuning

**Skills & Tags:**  
`SQL Server (T-SQL)`, `PostgreSQL`, `SQLite`, `Query Optimization`, `Covering Indexes`, `Pandera`, `Python Data Engineering`, `Pandas`, `ETL Pipelines`, `SQLAlchemy`, `Execution Plan Analysis`.

---

## 💼 Executive Project Overview

Engineered a reproducible, end-to-end data pipeline and relational query optimization benchmark on a **500,000-record synthetic enterprise transaction dataset**. This architecture demonstrates two core data engineering capabilities:
1. **Upstream Schema Hygiene (`etl_pipeline.py`):** A vectorized **Pandera Schema Gatekeeper** that validates 500,000 rows in **1.79 seconds (~279,173 rows/sec)**, automatically quarantining 150 injected schema violations (`quarantine_anomalies.csv`) and bulk-loading **499,850 clean rows** into the relational warehouse at **~98,386 rows/sec**.
2. **Empirical SQL Query Optimization (`run_sql_benchmark.py` & `optimization_benchmark.sql`):** Diagnosed and refactored critical SQL anti-patterns (non-SARGable scalar functions `UPPER()` / `SUBSTR()`, full table scans, and 3 correlated subqueries per row) into a **Composite Covering Index + Window Function CTE**, reducing measured execution latency from **8,857.91 ms to 101.12 ms (98.9% latency reduction / 87.6x speedup)** while returning the exact same **7,240 result rows**.

---

## 🛠️ Key Technical Deliverables

* **Vectorized ETL & Schema Contract Gatekeeper (`etl_pipeline.py`):**
  * Generates 500,000 synthetic transaction records with realistic regional distributions and 150 injected corruptions (negative amounts, invalid status domains, malformed order IDs).
  * Strict schema verification powered by `pandera.DataFrameSchema` with lazy validation (`lazy=True`) to capture all violations in a single pass.
  * Dead-Letter Quarantine isolation (`quarantine_anomalies.csv`) preserving zero-contamination staging tables.
  * High-velocity SQLAlchemy chunked insertion loading 499,850 validated rows into `enterprise_warehouse.db`.

* **Reproducible SQL Benchmark & T-SQL Index Architecture (`run_sql_benchmark.py` & `optimization_benchmark.sql`):**
  * Executes `EXPLAIN QUERY PLAN` and wall-clock benchmarks comparing the unoptimized vs. optimized queries on the 499,850-row database.
  * Designs composite covering indexes (`Region, Status, Transaction_Date` + included columns `Customer_ID, Amount, SKU, Order_ID`) eliminating full table scans (`SCAN o` -> `SEARCH o USING COVERING INDEX`).
  * Refactors correlated subqueries into single-pass analytical Window Functions (`ROW_NUMBER() OVER`, `SUM() OVER`).

---

## 📊 Empirical Measured Benchmark Results (499,850 Validated Rows)

| Benchmark Metric | Unoptimized Query (Before) | Optimized Query (After) | Measured Improvement |
| :--- | :--- | :--- | :--- |
| **Wall-Clock Execution Time** | **8,857.91 ms (8.86 s)** | **101.12 ms (0.101 s)** | **98.9% Latency Drop (87.6x Faster)** |
| **Primary Access Path** | `SCAN o` + `SCAN r` (Full Table Scans) | `SEARCH o USING COVERING INDEX` | **100% Index-Covered (Zero Table Lookups)** |
| **Aggregation & Ranking** | 3 Correlated Subqueries per Row | Single-Pass Window Functions CTE | **Eliminated Per-Row Nested Loops** |
| **Returned Result Set** | 7,240 Rows | 7,240 Rows | **100% Deterministic Output Parity** |
