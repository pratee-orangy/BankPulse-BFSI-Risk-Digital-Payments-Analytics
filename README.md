# BankPulse-BFSI-Risk-Digital-Payments-Analytics

The problem this project solves

India's UPI processes billions of transactions a month, but not all of them succeed. Some failures are the customer's fault (wrong PIN, insufficient funds). Others are the bank's own system failing.

BankPulse investigates: do banks with weaker financial health (higher Gross NPA ratios) also have less reliable UPI infrastructure (higher technical decline rates)? This is a real operational question that NPCI, RBI, and every fintech app integrating with these banks actually cares about.

Status

🚧 Work in progress — currently building the data pipeline (FY2024-25 snapshot). See Roadmap.

Business questions

Which banks have the best/worst UPI reliability (approval %, technical decline %, business decline %)?
Which banks combine high transaction volume with high decline rates — where does unreliability affect the most customers?
Which banks have the weakest asset quality (highest Gross NPA ratio)?
Is technical decline (the bank's own system failure) or business decline (user error) the bigger driver of failed transactions?
Core question: is there a visible relationship between Gross NPA ratio and UPI technical decline rate across banks? Which banks are outliers?


Scope note: this is currently a single fiscal-year (2024-25) cross-sectional snapshot, not a multi-year trend. Any relationship found is a correlation across banks at one point in time — not a causal claim.

Data sources

SourceDatasetGrainLinkNPCIUPI Remitter — Top 50 Member Performance (volume, value, approved %, BD%, TD%)Monthly, per banknpci.org.in/product/ecosystem-statistics/upiRBI DBIEMovement of Non-Performing Assets (Gross NPA opening/addition/reduction/write-off/closing)Annual (FY2024-25), per bankdata.rbi.org.in/DBIERBI DBIELiabilities and Assets of Scheduled Commercial Banks (Advances)Annual (FY2024-25), per bankdata.rbi.org.in/DBIE

All data is public and free to access. No proprietary or licensed data used.

Architecture

NPCI (UPI, monthly)  ─┐
RBI DBIE (NPA)        ├─→  Bronze (raw ingest)  →  Silver (cleaned, standardized)  →  Gold (business-ready)  →  Power BI
RBI DBIE (Advances)  ─┘


Bronze: raw files loaded as-is into Delta tables, no transformation
Silver: bank name standardization, type fixes, deduplication
Gold: gold_upi_annual, gold_bank_health, gold_combined — joined and aggregated to one row per bank
Power BI: star schema model, DAX measures, dashboard connected directly to Databricks


Tech stack

Azure Databricks (PySpark, Spark SQL, Delta Lake) — ingestion and transformation
Azure Data Lake Storage Gen2 — raw and processed data storage
Power BI (Power Query, DAX, Data Modeling) — dashboard and analysis
Python — data cleaning and bank-name standardization
SQL — Spark SQL transformations, aggregation logic
Git/GitHub — version control


Repo structure

├── data/
│   ├── raw/              # original downloaded files (bronze source)
│   └── processed/        # cleaned local reference copies (source of truth is Databricks)
├── notebooks/
│   ├── 01_bronze_ingest.py
│   ├── 02_silver_clean.py
│   └── 03_gold_transform.py
├── powerbi/
│   └── bankpulse_dashboard.pbix
├── docs/
│   └── data_dictionary.md
└── README.md

How it's built — phase by phase

PhaseWhatTool0Environment setup (workspace, storage, Power BI install)Azure Databricks, ADLS Gen2, Power BI Desktop1Organize and upload raw filesDatabricks UI / dbutils.fs2Bronze layer — raw ingestionPySpark3Silver layer — cleaning, bank name standardizationPySpark, Spark SQL4Gold layer — aggregation, NPA ratio calculation, joinsPySpark, Spark SQL5Connect Power BI to Databricks gold tablesPower BI Azure Databricks connector6Data modelingPower BI Model view7Measures and dashboard buildDAX8Documentation and publishingGitHub9Write-up and portfolio polishLinkedIn, Loom

Key finding

(To be added once analysis is complete.)

Roadmap

 Identify and download data sources
 Define business questions
 Build bronze ingestion notebook
 Standardize bank names across sources (silver layer)
 Compute Gross NPA ratio (NPA closing balance ÷ advances)
 Build gold layer combined table
 Connect Power BI to Databricks
 Build dashboard (Reliability Overview → Bank Health Overview → Combined/Correlation)
 Write up findings and add to this README
 Stretch goal: extend to multi-year trend (2–3 additional years of NPA/Advances data)


Honest limitations

Single fiscal year — no time-trend analysis yet
Bank-name matching across sources done via manual/rule-based mapping, not a verified master data source
Correlation shown does not imply causation

Skills demonstrated

Power BI · DAX · Data Modeling · Azure Databricks · PySpark · Delta Lake · Medallion Architecture · ETL · SQL · BFSI Analytics · Data Storytelling

Author

(Your name · LinkedIn · Portfolio link)
