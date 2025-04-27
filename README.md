# Hospital Patient

Project Description
This data engineering pipeline processes hospital patient and claims data from multiple sources (Hospital A and B), transforming raw healthcare records into structured, analytics-ready datasets. The system handles diverse medical data types including patient encounters, CPT-coded procedures, departmental workflows, provider details, insurance claims, and financial transactions.

The pipeline follows a multi-layered architecture (Bronze → Silver → Gold) to ensure data reliability and usability:
![Image 1](https://github.com/niha-v/Hospital-Patient/blob/main/Architecture%20-%20hospital%20patient.png)

Bronze Layer (Raw): Ingests heterogeneous data formats (CSV, JSON) and standardizes schemas while preserving raw fidelity.

Silver Layer (Cleaned): Applies critical healthcare-specific transformations—deduplicating patient records, validating insurance coverage, enriching provider data from NPI registries, and mapping medical codes (ICD-10, CPT) to standardized descriptions.

Gold Layer (Curated): Implements business logic for operational analytics, including temporal aggregations (YTD/MTD claims), readmission rate calculations, and financial performance metrics—optimized for downstream reporting.

## Key challenges addressed:

Data Consistency: Harmonizing disparate hospital systems (A & B) into a unified model.

Regulatory Compliance: Tracking historical changes via SCD2 for audits (e.g., patient address updates).

Clinical Relevance: Embedding medical coding standards (ICD, CPT) directly into the pipeline.

Built on Azure (ADF, Databricks, SQL Server) with SSIS orchestration, this pipeline feeds Power BI dashboards, EHR integrations, and reimbursement analysis tools—enabling data-driven decisions for hospital administrators and clinicians.


## Data Sources
- Claims Data (Hospital A & B)
- CPT Codes (Procedures)
- Departments
- Patient Encounters
- Provider Details
- Transaction Records

## Pipeline Steps
### 1. Raw Layer (Bronze) – Initial Ingestion
Source: CSV/JSON files from external systems.
Destination: Azure Blob Storage (Parquet format).

Transformations Applied
- *Schema Mapping* :
  - Convert CSV/JSON → Parquet (optimized storage).
  - Standardize column names (e.g., PatientID → patient_id).

- *Data Type Standardization*:
  - Dates → YYYY-MM-DD.
  - Numerical values → INT/FLOAT (e.g., claim_amount).

- *Deduplication*:
    Remove duplicates in:
  - patients (by patient_id).
  - transactions (by transaction_id).

- *Basic Validations*:
  - Ensure patient_id, provider_id, claim_id are NOT NULL.
  - Validate insurance coverage values (e.g., active = True/False).

  ### Raw Layer 
![Image](https://github.com/niha-v/Hospital-Patient/blob/main/Screenshot%202025-04-07%20103220.png)



### 2. Staging Layer (Silver) – Cleaning & Enrichment
Source: Bronze Layer (Azure Blob Storage).
Destination: SQL Server (via SSIS).
Transformations Applied

- *Data Cleaning*:
  - Fill missing provider details using NPI registry.
  - Map ICD-10 codes to diagnosis descriptions.

- *Slowly Changing Dimensions (SCD2)*:
  Track historical changes for:
  - Patients (address, PCP changes).
  - Transactions (status updates).
  - Claims (adjustments).

- *Columns*:
  - effective_date, end_date, is_current (boolean).

- *Common Data Model (CDM) Alignment*:
  - Standardize column names (e.g., encounter_date → visit_date).

- *Merge external reference data*:
  - NPI (provider details).
  - ICD-10 (diagnosis codes).
  - CPT (procedure codes).

### 3. Curated Layer (Gold) – Business Logic
Source: Silver Layer (SQL Server).
Destination: SQL Server (analytics-ready).
Transformations Applied

- *Aggregation*s:
  - YTD/MTD/QTD metrics

- *Analytics Optimizations*:
  - Create fact/dimension tables (star schema).
  - Pre-calculate KPIs (e.g., avg. claim processing time).
    
 ### The complete Pipeline from Raw to Curated Layer 
![Image 2](https://github.com/niha-v/Hospital-Patient/blob/main/raw%20to%20curated)

## Technology Stack
### Layer	       |   Tools

- Ingestion	     -  Azure Data Factory (ADF)
- Storage      	 -  Azure Blob Storage (Bronze), SQL Server (Silver/Gold)
- Processing	   -  Azure Databricks (PySpark/SQL)
- Orchestration	 -  SSIS
- Analytics	     -  Power BI, SQL Server Reporting

---









*©niharika.umrani*
---
