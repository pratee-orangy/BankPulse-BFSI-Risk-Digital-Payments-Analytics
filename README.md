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



--------------------------------------------------------------------------------------




A data analytics project using Power BI and Azure Databricks, built on real Indian banking and UPI data.

What is this project about?

You know how sometimes a UPI payment just... fails? Sometimes it's your fault (wrong PIN, not enough balance). But sometimes it's the bank's own system messing up.

I got curious: do banks that are struggling financially (lots of bad loans) also have shakier UPI systems? Like, does a bank's money trouble show up in how often your payment fails?

So I took real UPI transaction data (from NPCI) and real bank financial data (from RBI) and tried to find out.

Status

🚧 Still building this. Right now I'm setting up the data pipeline. Check the to-do list below to see where I'm at.

What I'm actually trying to find out


Which banks are best/worst at processing UPI payments successfully?
Which big banks (the ones lots of people use) also have bad failure rates? That matters more than a small bank messing up.
Which banks have the most bad loans (financially weakest)?
When a payment fails, is it usually the bank's fault (technical issue) or the user's fault (wrong PIN, low balance)?
The main question: do weaker banks (more bad loans) also fail more UPI payments? And which banks break that pattern?


Being honest: I only have one year of data right now, so this shows a snapshot, not a trend over time. And even if I find a pattern, it doesn't prove one thing causes the other — just that they show up together.

Where the data comes from

SourceWhat it isHow oftenWhere to get itNPCIBank-wise UPI performance — how many payments succeeded, failed, whyMonthlynpci.org.in/product/ecosystem-statistics/upiRBIBad loans (NPAs) for each bankYearly (FY2024-25)data.rbi.org.in/DBIERBITotal loans given out by each bank (needed to calculate the bad-loan %)Yearly (FY2024-25)data.rbi.org.in/DBIE

Everything here is free, public data. Nothing paid or private.

How the data flows

UPI data (monthly)  ─┐
Bad loan data         ├─→  Raw data in  →  Clean it up  →  Combine into useful tables  →  Power BI dashboard
Total loans data     ─┘

In simple terms:


Raw: dump the files in exactly as downloaded, don't touch anything yet
Clean: fix messy bank names, fix number formats, remove junk
Combine: turn 12 months of UPI data into one row per bank, calculate each bank's bad-loan percentage, then join everything together into one table per bank
Dashboard: connect that final table to Power BI and build the actual charts


Tools I'm using


Azure Databricks — this is where the cleaning and combining happens (using Python/PySpark)
Azure Data Lake Storage — where the files actually live
Power BI — where the dashboard gets built
Python — for cleaning up messy data (like fixing bank names)
SQL — for the actual calculations and joins
GitHub — to keep track of everything as I build it


How the project is organized

├── data/
│   ├── raw/              # files exactly as downloaded
│   └── processed/        # cleaned versions
├── notebooks/
│   ├── 01_bronze_ingest.py     # step 1: load raw data
│   ├── 02_silver_clean.py      # step 2: clean it
│   └── 03_gold_transform.py    # step 3: combine and calculate
├── powerbi/
│   └── bankpulse_dashboard.pbix
├── docs/
│   └── data_dictionary.md
└── README.md

What I found (so far)

Nothing yet — I'll fill this in once the dashboard is actually done. Not writing a "finding" before I've actually found something.

To-do list


 Figure out what data I need and where to get it
 Decide what questions I'm actually answering
 Load the raw files into Databricks
 Clean up bank names so everything matches across files
 Calculate each bank's bad-loan percentage
 Combine everything into one final table
 Connect Power BI to Databricks
 Build the dashboard
 Write up what I actually found
 Maybe later: pull 2-3 more years of data to see if the pattern holds over time


Things I'm being upfront about


Only one year of data right now, so this isn't a trend, it's a snapshot
I matched bank names across files manually, not with some official master list — so there's a small chance of mismatches
If I find a pattern, it just means two things happen together, not that one causes the other


Skills this project shows

Power BI, DAX, data modeling, Azure Databricks, PySpark, Delta Lake, ETL pipelines, SQL, banking/fintech data analysis

About me

(name, LinkedIn, portfolio link)

(Your name · LinkedIn · Portfolio link)
