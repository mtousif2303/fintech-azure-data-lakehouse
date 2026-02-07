# 🏦 Fintech Azure Data Lakehouse

Tech Stack : Python, Azure SQL Database, SQL, Azure Synapse, ADLS, Logic Apps, PySpark, Delta Tables

A production-ready, enterprise-grade data engineering solution implementing a complete medallion architecture (Bronze-Silver-Gold) for fintech data processing on Azure Cloud. This project demonstrates modern data lakehouse patterns with ACID transactions, incremental processing, and automated orchestration

## 🎯 Project Overview

This project implements a **complete end-to-end data engineering pipeline** that migrates historical fintech data from Azure SQL Database to Azure Data Lake Storage using the **Medallion Architecture** pattern. The solution leverages **Azure Synapse Analytics**, **PySpark**, and **Delta Lake** to provide:


a)Azure SQL Database

<img width="3072" height="1764" alt="image" src="https://github.com/user-attachments/assets/43bdf56c-128f-4e0e-ba93-3020b0c17325" />

b) ADLS

<img width="3104" height="1598" alt="image" src="https://github.com/user-attachments/assets/8dcd3613-eb59-4c97-a880-cb473efb609a" />


c) Fintech Pipeline

<img width="3436" height="2018" alt="image" src="https://github.com/user-attachments/assets/ffa4d984-1e3a-46bd-b89f-7f7a17995248" />


d) Success notification

<img width="1724" height="561" alt="image" src="https://github.com/user-attachments/assets/3a7b73c1-1ff4-44b7-ab93-cf9a77c7ac0d" />


<img width="3438" height="1532" alt="image" src="https://github.com/user-attachments/assets/8d628390-27f3-4b97-9ea4-fbd4d8e9b116" />

e) Delta tables

<img width="3442" height="1532" alt="image" src="https://github.com/user-attachments/assets/b5081480-040d-489e-8c73-feaa3bdd6be3" />

<img width="3446" height="1282" alt="image" src="https://github.com/user-attachments/assets/75d2807b-d00d-4582-a049-bfa375f492b2" />




## 🏗️ Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         AZURE CLOUD PLATFORM                             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  ┌─────────────────┐                                                     │
│  │  Azure SQL DB   │                                                     │
│  │  (Source Data)  │                                                     │
│  │                 │                                                     │
│  │ • Customers     │                                                     │
│  │ • Accounts      │                                                     │
│  │ • Loans         │                                                     │
│  │ • Payments      │                                                     │
│  │ • Transactions  │                                                     │
│  └────────┬────────┘                                                     │
│           │                                                              │
│           │ Dynamic Ingestion                                            │
│           ▼                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │           AZURE SYNAPSE ANALYTICS WORKSPACE                      │    │
│  ├─────────────────────────────────────────────────────────────────┤    │
│  │                                                                  │    │
│  │  ┌──────────────────────────────────────────────────────────┐   │    │
│  │  │         ORCHESTRATION PIPELINE (Master)                   │   │    │
│  │  ├──────────────────────────────────────────────────────────┤   │    │
│  │  │  1. Get Table List (Lookup Activity)                     │   │    │
│  │  │  2. ForEach Table (Parallel Processing)                  │   │    │
│  │  │     └─> Copy to Bronze Layer                             │   │    │
│  │  │  3. Bronze → Silver Transformation (Notebook)            │   │    │
│  │  │  4. Silver → Gold Transformation (Notebook)              │   │    │
│  │  │  5. Success/Failure Notifications (Web Activity)         │   │    │
│  │  └──────────────────────────────────────────────────────────┘   │    │
│  │                                                                  │    │
│  │  ┌──────────────────────────────────────────────────────────┐   │    │
│  │  │         APACHE SPARK POOL (Compute)                       │   │    │
│  │  ├──────────────────────────────────────────────────────────┤   │    │
│  │  │  • Node Size: Small (4 vCores, 32GB RAM)                 │   │    │
│  │  │  • Auto-scale: 3-10 nodes                                │   │    │
│  │  │  • Auto-pause: 15 minutes idle                           │   │    │
│  │  │  • Spark Version: 3.4                                    │   │    │
│  │  └──────────────────────────────────────────────────────────┘   │    │
│  │                                                                  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│           │                                                              │
│           │ Write Data                                                   │
│           ▼                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │     AZURE DATA LAKE STORAGE GEN2 (fintechdatagdsde)            │    │
│  ├─────────────────────────────────────────────────────────────────┤    │
│  │  Container: fintech                                             │    │
│  │                                                                  │    │
│  │  ┌────────────────────────────────────────────────────────┐     │    │
│  │  │  📁 bronze/  (Raw Data - Parquet)                      │     │    │
│  │  │     ├─ Accounts/                                       │     │    │
│  │  │     ├─ Customers/                                      │     │    │
│  │  │     ├─ Loans/                                          │     │    │
│  │  │     ├─ Payments/                                       │     │    │
│  │  │     └─ Transactions/                                   │     │    │
│  │  └────────────────────────────────────────────────────────┘     │    │
│  │           │ Cleanse, Validate, Enrich                            │    │
│  │           ▼                                                      │    │
│  │  ┌────────────────────────────────────────────────────────┐     │    │
│  │  │  📁 silver/  (Cleaned Data - Delta Tables)            │     │    │
│  │  │     ├─ Accounts/                                       │     │    │
│  │  │     │   └─ _delta_log/  (Transaction Log)             │     │    │
│  │  │     ├─ Customers/                                      │     │    │
│  │  │     ├─ Loans/                                          │     │    │
│  │  │     ├─ Payments/                                       │     │    │
│  │  │     └─ Transactions/                                   │     │    │
│  │  └────────────────────────────────────────────────────────┘     │    │
│  │           │ Aggregate, Model, Optimize                           │    │
│  │           ▼                                                      │    │
│  │  ┌────────────────────────────────────────────────────────┐     │    │
│  │  │  📁 gold/  (Business-Ready - Star Schema)             │     │    │
│  │  │     ├─ dim_customers/      (Dimension)                │     │    │
│  │  │     ├─ dim_accounts/       (Dimension)                │     │    │
│  │  │     ├─ dim_loans/          (Dimension)                │     │    │
│  │  │     ├─ fact_payments/      (Fact Table)               │     │    │
│  │  │     └─ fact_transactions/  (Fact Table)               │     │    │
│  │  └────────────────────────────────────────────────────────┘     │    │
│  │                                                                  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### Medallion Architecture Layers

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      MEDALLION ARCHITECTURE                              │
└─────────────────────────────────────────────────────────────────────────┘

🥉 BRONZE LAYER (Raw/Landing Zone)
┌────────────────────────────────────────────────────────────────┐
│ Purpose:   Raw data ingestion with minimal transformation      │
│ Format:    Parquet (compressed, columnar)                      │
│ Schema:    Schema from source (SQL Database)                   │
│ Quality:   No validation, data as-is from source               │
│ Retention: Full historical data, immutable                     │
└────────────────────────────────────────────────────────────────┘
                              │
                              │ PySpark Transformation
                              │ • Remove duplicates
                              │ • Standardize formats
                              │ • Add metadata columns
                              │ • Data quality checks
                              ▼
🥈 SILVER LAYER (Cleansed/Conformed Zone)
┌────────────────────────────────────────────────────────────────┐
│ Purpose:   Validated, cleaned, enriched data                   │
│ Format:    Delta Lake (ACID, versioned)                        │
│ Schema:    Conformed schema with data types enforced           │
│ Quality:   Validated, deduplicated, standardized               │
│ Features:  • Time travel capability                            │
│            • Schema evolution support                          │
│            • ACID transactions                                 │
└────────────────────────────────────────────────────────────────┘
                              │
                              │ PySpark Transformation
                              │ • Dimensional modeling
                              │ • Denormalization
                              │ • Business logic
                              │ • Aggregations
                              ▼
🥇 GOLD LAYER (Business/Consumption Zone)
┌────────────────────────────────────────────────────────────────┐
│ Purpose:   Analytics-ready, dimensional model                  │
│ Format:    Delta Lake (optimized for queries)                  │
│ Schema:    Star schema (Facts + Dimensions)                    │
│ Quality:   Business-validated, aggregated                      │
│ Features:  • Optimized for BI tools                            │
│            • Pre-aggregated metrics                            │
│            • Low latency queries                               │
└────────────────────────────────────────────────────────────────┘
```

### Data Flow Diagram

```
SQL Database              Bronze Layer           Silver Layer           Gold Layer
─────────────            ─────────────          ─────────────         ─────────────

┌─────────┐              ┌─────────┐            ┌─────────┐          ┌──────────┐
│Customer │─────────────▶│Customer │───────────▶│Customer │─────────▶│   dim_   │
│  Table  │   Copy       │ Parquet │  Cleanse   │  Delta  │  Model   │customers │
└─────────┘              └─────────┘            └─────────┘          └──────────┘
                                                                             │
┌─────────┐              ┌─────────┐            ┌─────────┐                 │
│Account  │─────────────▶│Account  │───────────▶│Account  │─────────▶┌──────▼───┐
│  Table  │              │ Parquet │            │  Delta  │          │   dim_   │
└─────────┘              └─────────┘            └─────────┘          │accounts  │
                                                      │               └──────────┘
                                                      │                      │
┌─────────┐              ┌─────────┐            ┌────▼────┐                 │
│  Loan   │─────────────▶│  Loan   │───────────▶│  Loan   │─────────▶┌──────▼───┐
│  Table  │              │ Parquet │            │  Delta  │          │   dim_   │
└─────────┘              └─────────┘            └─────────┘          │  loans   │
                                                      │               └──────────┘
                                                      │                      │
┌─────────┐              ┌─────────┐            ┌────▼────┐          ┌──────▼────┐
│Payment  │─────────────▶│Payment  │───────────▶│Payment  │─────────▶│   fact_   │
│  Table  │              │ Parquet │            │  Delta  │   Join   │ payments  │
└─────────┘              └─────────┘            └─────────┘          └───────────┘

┌─────────┐              ┌─────────┐            ┌─────────┐          ┌───────────┐
│Trans-   │─────────────▶│Trans-   │───────────▶│Trans-   │─────────▶│   fact_   │
│action   │              │action   │            │action   │   Join   │transaction│
└─────────┘              └─────────┘            └─────────┘          └───────────┘
```

---

## 🛠️ Tech Stack

### Cloud & Infrastructure
| Technology | Purpose | Version |
|------------|---------|---------|
| **Microsoft Azure** | Cloud Platform | Latest |
| **Azure Synapse Analytics** | Unified Analytics Platform | Workspace |
| **Azure Data Lake Storage Gen2** | Scalable Data Storage | Gen2 |
| **Azure SQL Database** | Source Database | Standard Tier |

### Data Processing
| Technology | Purpose | Version |
|------------|---------|---------|
| **Apache Spark** | Distributed Processing | 3.4 |
| **PySpark** | Spark Python API | 3.4 |
| **Delta Lake** | ACID Storage Layer | 2.4+ |
| **Parquet** | Columnar Storage Format | Latest |

### Languages & Tools
| Technology | Purpose | Version |
|------------|---------|---------|
| **Python** | Primary Language | 3.11 |
| **SQL (T-SQL)** | Data Definition & Queries | SQL Server 2019+ |
| **JSON** | Configuration Files | - |

---

- ✅ **Scalable data ingestion** from relational databases
- ✅ **Progressive data refinement** through Bronze → Silver → Gold layers
- ✅ **ACID transactions** with Delta Lake format
- ✅ **Automated orchestration** with error handling and notifications
- ✅ **Data quality validation** at each transformation stage
- ✅ **Dimensional modeling** for analytics workloads

### Use Case

The pipeline processes fintech data including:
- **Customer information** (demographics, contact details)
- **Account management** (balances, account types, age)
- **Loan portfolios** (amounts, interest rates, durations)
- **Payment transactions** (payment history, methods)
- **Financial transactions** (deposits, withdrawals, transfers)

---

## 📊 Data Model

### Source Schema (Azure SQL Database)

```sql
-- 5 Tables with Relationships

Customers (5,000 records)
├─ CustomerID (PK)
├─ FirstName, LastName
├─ Email, PhoneNumber
├─ Address, City, State, Country, ZipCode
└─ SignupDate

Accounts (10,000 records)
├─ AccountID (PK)
├─ CustomerID (FK → Customers)
├─ AccountType (Checking, Savings, Credit, Investment)
├─ Balance
├─ OpenDate
└─ AccountAgeYears

Loans (8,000 records)
├─ LoanID (PK)
├─ CustomerID (FK → Customers)
├─ LoanType (Personal, Home, Auto, Business, Student)
├─ LoanAmount
├─ InterestRate
├─ LoanStartDate, LoanEndDate
├─ TotalInterest
└─ LoanDurationYears

Payments (50,000 records)
├─ PaymentID (PK)
├─ LoanID (FK → Loans)
├─ PaymentDate
├─ PaymentAmount
└─ PaymentMethod (Credit Card, Debit Card, ACH, Wire Transfer, Check)

Transactions (100,000 records)
├─ TransactionID (PK)
├─ AccountID (FK → Accounts)
├─ TransactionDate
├─ Amount
├─ TransactionType (Deposit, Withdrawal, Transfer, Fee)
└─ Description
```

### Target Schema (Gold Layer - Star Schema)

```sql
-- Dimension Tables (slowly changing dimensions)

dim_customers
├─ customer_id (PK)
├─ first_name, last_name
├─ email, phone_number
├─ address, city, state, country, zip_code
└─ signup_date

dim_accounts
├─ account_id (PK)
├─ customer_id (FK)
├─ account_type
├─ balance
├─ open_date
└─ account_age_years

dim_loans
├─ loan_id (PK)
├─ customer_id (FK)
├─ loan_type
├─ loan_amount
├─ interest_rate
├─ loan_start_date, loan_end_date
├─ total_interest
└─ loan_duration_years

-- Fact Tables (transaction facts)

fact_payments
├─ payment_id (PK)
├─ loan_id (FK → dim_loans)
├─ customer_id (FK → dim_customers)
├─ payment_date
├─ payment_amount
└─ payment_method

fact_transactions
├─ transaction_id (PK)
├─ account_id (FK → dim_accounts)
├─ customer_id (FK → dim_customers)
├─ transaction_date
├─ amount
├─ transaction_type
└─ description
```

---

## 📁 Project Structure

```
fintech-azure-data-lakehouse/
│
├── 📂 sql/                              # Database scripts
│   ├── create_tables.sql                # DDL for source tables
│   ├── insert_sample_data.sql           # Sample data generation
│   └── schema_diagram.md                # ER diagram documentation
│
├── 📂 synapse/                          # Synapse artifacts
│   ├── 📂 pipelines/
│   │   ├── FintechPipeline.json         # Master orchestration pipeline
│   │   ├── bronze_ingestion.json        # Dynamic data copy pipeline
│   │   └── pipeline_parameters.md       # Parameter documentation
│   │
│   ├── 📂 notebooks/
│   │   ├── bronze_to_silver.ipynb       # Bronze → Silver transformation
│   │   ├── silver_to_gold.ipynb         # Silver → Gold transformation
│   │   └── data_quality_checks.ipynb    # Validation notebook
│   │
│   ├── 📂 datasets/
│   │   ├── sql_source.json              # SQL Database dataset
│   │   ├── adls_bronze.json             # Bronze layer dataset
│   │   └── adls_delta.json              # Delta lake dataset
│   │
│   └── 📂 linked_services/
│       ├── sql_database_ls.json         # SQL DB connection
│       └── adls_ls.json                 # ADLS connection
│
├── 📂 notebooks/                        # Local development notebooks
│   ├── bronze_to_silver_transform.py    # Silver transformation logic
│   ├── silver_to_gold_transform.py      # Gold transformation logic
│   └── utils.py                         # Reusable functions
│
├── 📂 config/
│   ├── pipeline_config.json             # Pipeline configuration
│   ├── spark_config.json                # Spark settings
│   └── environment_vars.env             # Environment variables template
│
├── 📂 docs/
│   ├── setup_guide.md                   # Detailed setup instructions
│   ├── architecture.md                  # Architecture deep-dive
│   ├── troubleshooting.md               # Common issues & fixes
│   └── best_practices.md                # Engineering best practices
│
├── 📂 tests/
│   ├── test_transformations.py          # Unit tests
│   └── test_data_quality.py             # Data quality tests
│
├── .gitignore                           # Git ignore rules
├── README.md                            # This file
├── requirements.txt                     # Python dependencies
└── LICENSE                              # License information
```

---

## 🚀 Features

### Core Capabilities

✅ **Dynamic Schema Detection**
- Automatically discovers all tables in SQL Database
- No hardcoding of table names or schemas
- Scalable to new tables without pipeline changes

✅ **Medallion Architecture Implementation**
- **Bronze Layer**: Raw data preservation with full fidelity
- **Silver Layer**: Cleaned, validated, Delta Lake tables
- **Gold Layer**: Business-ready dimensional model (star schema)

✅ **Delta Lake Integration**
- ACID transactions for data reliability
- Time travel for auditing and rollback
- Schema evolution for flexibility
- Optimized file layouts with Z-ordering

✅ **Parallel Processing**
- ForEach loop for concurrent table ingestion
- Distributed Spark processing for transformations
- Auto-scaling Spark pool (3-10 nodes)

✅ **Data Quality Framework**
- Null value handling and validation
- Duplicate detection and removal
- Data type enforcement
- Business rule validation

✅ **Error Handling & Notifications**
- Try-catch blocks in transformations
- Detailed logging with timestamps
- Success/failure notifications via webhooks
- Pipeline retry logic

✅ **Monitoring & Observability**
- Pipeline execution metrics
- Spark job monitoring
- Data lineage tracking
- Audit logs and history

---

## 🔧 Prerequisites

### Azure Resources Required

| Resource | SKU/Tier | Purpose |
|----------|----------|---------|
| **Azure Subscription** | Pay-as-you-go or Enterprise | Host all resources |
| **Resource Group** | - | Logical container for resources |
| **Azure SQL Database** | Standard S2 or higher | Source data storage |
| **Azure Synapse Workspace** | - | Orchestration & transformation |
| **Spark Pool** | Small (4 vCores) | Compute for notebooks |
| **ADLS Gen2 Storage** | Standard LRS | Data lake storage |

### Software & Tools

- **Azure CLI** (v2.50+) - For resource deployment
- **Python** (3.8+) - For local development
- **Git** - Version control
- **VS Code** (optional) - Code editor with Azure extensions
- **SQL Server Management Studio** (optional) - Database management

### Required Permissions

- **Contributor** role on Resource Group
- **Storage Blob Data Contributor** on ADLS Gen2
- **Synapse Administrator** role on Synapse Workspace
- **db_datareader** on SQL Database

---

## 📦 Setup Guide

### Step 1: Clone the Repository

```bash
git clone https://github.com/mtousif2303/fintech-azure-data-lakehouse.git
cd fintech-azure-data-lakehouse
```

### Step 2: Create Azure Resources

#### Option A: Using Azure Portal (Manual)

1. **Create Resource Group**
   ```
   Name: rg-fintech-datalake
   Region: East US 2 (or your preferred region)
   ```

2. **Create Azure SQL Database**
   ```
   Server name: sql-fintech-<yourname>
   Database name: fintechdb
   Authentication: SQL Authentication
   Admin username: sqladmin
   Pricing tier: Standard S2 (50 DTUs)
   ```

3. **Create Storage Account (ADLS Gen2)**
   ```
   Storage account name: fintechdatagdsde
   Performance: Standard
   Redundancy: LRS
   ✅ Enable hierarchical namespace (CRITICAL for ADLS Gen2)
   
   Create container: fintech
   Create folders: bronze/, silver/, gold/
   ```

4. **Create Synapse Workspace**
   ```
   Workspace name: synapse-fintech-<yourname>
   ADLS Gen2 account: fintechdatagdsde
   File system: fintech
   
   Create Apache Spark pool:
   - Name: sparkpool
   - Node size: Small (4 vCores, 32 GB)
   - Autoscale: Enabled (3-10 nodes)
   - Auto-pause: 15 minutes
   ```

#### Option B: Using Azure CLI (Automated)

```bash
# Login to Azure
az login

# Set variables
RG_NAME="rg-fintech-datalake"
LOCATION="eastus2"
SQL_SERVER="sql-fintech-$(whoami)"
SQL_DB="fintechdb"
SQL_ADMIN="sqladmin"
SQL_PASSWORD="YourStrongP@ssw0rd123!"
STORAGE_ACCOUNT="fintechdatagdsde"
SYNAPSE_WORKSPACE="synapse-fintech-$(whoami)"

# Create resource group
az group create --name $RG_NAME --location $LOCATION

# Create SQL Server and Database
az sql server create \
  --name $SQL_SERVER \
  --resource-group $RG_NAME \
  --location $LOCATION \
  --admin-user $SQL_ADMIN \
  --admin-password $SQL_PASSWORD

az sql db create \
  --resource-group $RG_NAME \
  --server $SQL_SERVER \
  --name $SQL_DB \
  --service-objective S2

# Allow Azure services to access SQL Server
az sql server firewall-rule create \
  --resource-group $RG_NAME \
  --server $SQL_SERVER \
  --name AllowAzureServices \
  --start-ip-address 0.0.0.0 \
  --end-ip-address 0.0.0.0

# Create Storage Account (ADLS Gen2)
az storage account create \
  --name $STORAGE_ACCOUNT \
  --resource-group $RG_NAME \
  --location $LOCATION \
  --sku Standard_LRS \
  --kind StorageV2 \
  --hierarchical-namespace true

# Create container
az storage container create \
  --name fintech \
  --account-name $STORAGE_ACCOUNT \
  --auth-mode login

# Create Synapse Workspace
az synapse workspace create \
  --name $SYNAPSE_WORKSPACE \
  --resource-group $RG_NAME \
  --location $LOCATION \
  --storage-account $STORAGE_ACCOUNT \
  --file-system fintech \
  --sql-admin-login-user $SQL_ADMIN \
  --sql-admin-login-password $SQL_PASSWORD

# Create Spark Pool
az synapse spark pool create \
  --name sparkpool \
  --workspace-name $SYNAPSE_WORKSPACE \
  --resource-group $RG_NAME \
  --spark-version 3.4 \
  --node-count 3 \
  --node-size Small \
  --enable-auto-scale true \
  --max-node-count 10 \
  --enable-auto-pause true \
  --delay 15
```

### Step 3: Setup SQL Database

```bash
# Connect to SQL Database
sqlcmd -S $SQL_SERVER.database.windows.net -d $SQL_DB -U $SQL_ADMIN -P $SQL_PASSWORD

# Or use Azure Data Studio / SSMS
```

Execute the SQL scripts in order:

```sql
-- 1. Create tables
:r sql/create_tables.sql

-- 2. Insert sample data
:r sql/insert_sample_data.sql

-- 3. Verify data
SELECT 'Customers' AS TableName, COUNT(*) AS RowCount FROM Customers
UNION ALL
SELECT 'Accounts', COUNT(*) FROM Accounts
UNION ALL
SELECT 'Loans', COUNT(*) FROM Loans
UNION ALL
SELECT 'Payments', COUNT(*) FROM Payments
UNION ALL
SELECT 'Transactions', COUNT(*) FROM Transactions;
```

### Step 4: Configure Synapse Linked Services

1. **Open Synapse Studio**
   - Navigate to: https://web.azuresynapse.net
   - Select your workspace

2. **Create SQL Database Linked Service**
   ```
   Manage → Linked services → + New
   
   Type: Azure SQL Database
   Name: LS_AzureSQLDatabase
   Server name: <your-sql-server>.database.windows.net
   Database name: fintechdb
   Authentication: SQL authentication
   Username: sqladmin
   Password: <your-password>
   
   Test connection → Create
   ```

3. **Create ADLS Gen2 Linked Service**
   ```
   Manage → Linked services → + New
   
   Type: Azure Data Lake Storage Gen2
   Name: LS_AzureDataLakeStorage
   Storage account name: fintechdatagdsde
   Authentication: Account key (or Managed Identity)
   
   Test connection → Create
   ```

### Step 5: Upload and Configure Notebooks

1. **Upload Notebooks**
   ```
   Develop → + → Import
   Select: notebooks/bronze_to_silver.ipynb
   Select: notebooks/silver_to_gold.ipynb
   ```

2. **Update Configuration in Each Notebook**
   
   **Bronze to Silver Notebook:**
   ```python
   # Update these paths
   bronze_base_path = "abfss://fintech@fintechdatagdsde.dfs.core.windows.net/bronze/"
   silver_base_path = "abfss://fintech@fintechdatagdsde.dfs.core.windows.net/silver/"
   ```

   **Silver to Gold Notebook:**
   ```python
   # Update these paths
   silver_base_path = "abfss://fintech@fintechdatagdsde.dfs.core.windows.net/silver/"
   gold_base_path = "abfss://fintech@fintechdatagdsde.dfs.core.windows.net/gold/"
   ```

3. **Attach Spark Pool**
   - Select notebook → Attach to → sparkpool

### Step 6: Create and Configure Pipeline

1. **Import Pipeline**
   ```
   Integrate → + → Import from pipeline template
   Select: synapse/pipelines/FintechPipeline.json
   ```

2. **Configure Activities**

   **Lookup Activity (getTableList):**
   ```
   Settings:
   - Source dataset: Create new (Azure SQL Database)
   - Query: 
     SELECT TABLE_NAME 
     FROM INFORMATION_SCHEMA.TABLES 
     WHERE TABLE_TYPE = 'BASE TABLE' 
     AND TABLE_NAME IN ('Customers','Accounts','Loans','Payments','Transactions')
   ```

   **ForEach Activity (ForEach1):**
   ```
   Settings:
   - Items: @activity('getTableList').output.value
   - Batch count: 5 (parallel execution)
   ```

   **Copy Activity (CopyToBronzeLayer):**
   ```
   Source:
   - Source dataset: SQL Database
   - Table name: @item().TABLE_NAME
   
   Sink:
   - Sink dataset: ADLS Gen2
   - File path: bronze/@{item().TABLE_NAME}/
   - File format: Parquet
   - Compression: Snappy
   ```

   **Notebook Activities:**
   ```
   Bronze to Silver:
   - Notebook: bronze_to_silver
   - Spark pool: sparkpool
   
   Silver to Gold:
   - Notebook: silver_to_gold
   - Spark pool: sparkpool
   ```

3. **Setup Error Handling**
   - Add failure paths from each activity
   - Connect to failedNotification web activity
   - Configure webhook URL for notifications

### Step 7: Test the Pipeline

```bash
# Trigger manual run
Integrate → FintechPipeline → Add trigger → Trigger now

# Monitor execution
Monitor → Pipeline runs
```

---

## 🎯 Pipeline Execution

### Manual Execution

1. Navigate to **Synapse Studio**
2. Go to **Integrate** → **Pipelines**
3. Select **FintechPipeline**
4. Click **Add trigger** → **Trigger now**
5. Monitor in **Monitor** → **Pipeline runs**

### Scheduled Execution

```
Integrate → FintechPipeline → Add trigger → New/Edit

Trigger settings:
- Name: DailyIngestion
- Type: Schedule
- Recurrence: Daily
- Start time: 02:00 AM UTC
- End: No end
- Time zone: UTC

Save and Publish
```

### Pipeline Execution Flow

```
Start
  │
  ▼
┌─────────────────────┐
│ 1. getTableList     │  Lookup Activity
│    (17s)            │  Queries INFORMATION_SCHEMA
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 2. ForEach1         │  ForEach Activity
│    (44s)            │  Parallel: 5 tables
├─────────────────────┤
│  CopyToBronzeLayer  │  Copy Data × 5
│  (18-42s each)      │  SQL → Parquet
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 3. bronzeToSilver   │  Notebook Activity
│    Process          │  PySpark Transformation
│    (6m 7s)          │  Parquet → Delta
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ 4. silverToGold     │  Notebook Activity
│    Process          │  PySpark Transformation
│    (1m 30s)         │  Star Schema Creation
└──────────┬──────────┘
           │
           ├─ Success ──▶ successNotification (Web Activity)
           │
           └─ Failure ──▶ failedNotification (Web Activity)
```

---

## ⚠️ Issues Faced & Solutions

### Issue #1: PATH_NOT_FOUND Error in Silver to Gold Notebook

**Error Message:**
```
AnalysisException: [PATH_NOT_FOUND] Path does not exist: 
abfss://fintech@fintechdatagdsde.dfs.core.windows.net/silver/Accounts
```

**Root Cause:**
The Bronze to Silver notebook was writing data as **Parquet files** instead of **Delta Lake format**. The Silver to Gold notebook expected Delta tables with `_delta_log` directories, which didn't exist.

**Investigation Steps:**
1. Checked folder existence in Azure Storage Explorer ✅ (folders existed)
2. Listed folder contents using `mssparkutils.fs.ls()` ✅ (files present)
3. Checked for `_delta_log/` directory ❌ (missing - not Delta format)
4. Attempted to read as Parquet ✅ (succeeded)
5. Attempted to read as Delta ❌ (failed)

**Solution:**
Updated Bronze to Silver notebook write statement:

```python
# ❌ WRONG - Writes Parquet, not Delta
df.write.mode("overwrite").save(f"{silver_base_path}Accounts/")

# ✅ CORRECT - Writes Delta Lake format
df.write.format("delta").mode("overwrite").save(f"{silver_base_path}Accounts/")
```

**Prevention:**
- Always explicitly specify `.format("delta")` when writing to Silver/Gold layers
- Add validation checks to verify Delta log existence
- Use consistent format specifications across all notebooks

---

### Issue #2: NameError - 'dbutils' is not defined

**Error Message:**
```python
NameError: name 'dbutils' is not defined
```

**Root Cause:**
Used Databricks-specific `dbutils` in Azure Synapse environment. Synapse uses `mssparkutils` instead.

**Solution:**
Replace all `dbutils` references with `mssparkutils`:

```python
# ❌ Databricks
folders = dbutils.fs.ls(path)
dbutils.fs.rm(path, recurse=True)

# ✅ Azure Synapse
folders = mssparkutils.fs.ls(path)
mssparkutils.fs.rm(path, recurse=True)
```

**Key Differences:**

| Databricks | Azure Synapse |
|------------|---------------|
| `dbutils.fs.ls()` | `mssparkutils.fs.ls()` |
| `dbutils.notebook.run()` | `mssparkutils.notebook.run()` |
| `dbutils.secrets.get()` | `mssparkutils.credentials.getSecret()` |
| `folder.isDir()` (method) | `folder.isDir` (property) |

---

### Issue #3: Notebook Variables Not Defined Between Cells

**Error Message:**
```python
NameError: name 'customers_df' is not defined
```

**Root Cause:**
Split transformation logic across multiple notebook cells, but cells were run in separate sessions or out of order.

**Solution:**
Consolidated all transformation logic into a single cell or ensured proper cell execution order:

```python
# Cell 1: Load data
accounts_df = spark.read.format("delta").load(path)
customers_df = spark.read.format("delta").load(path)

# Cell 2: Transform (can only run after Cell 1)
dim_customers = customers_df.select(...)
```

**Best Practice:**
For production pipelines, use a single comprehensive cell or create proper dependencies with error handling.

---

### Issue #4: Schema Mismatch - Column Not Found

**Error Message:**
```
AnalysisException: Column 'CustomerID' does not exist
```

**Root Cause:**
Source data had inconsistent column naming (some tables used `customer_id`, others `CustomerID`).

**Solution:**
Added schema validation and standardization:

```python
# Standardize column names
df = df.toDF(*[col.lower() for col in df.columns])

# Or handle case-insensitive
from pyspark.sql.functions import col
df.select(col("CustomerID").alias("customer_id"))
```

---

### Issue #5: Permission Denied on ADLS

**Error Message:**
```
java.nio.file.AccessDeniedException: Operation failed: 
"This request is not authorized to perform this operation"
```

**Root Cause:**
Synapse workspace managed identity lacked proper permissions on ADLS Gen2.

**Solution:**
1. Navigate to Azure Portal → Storage Account
2. Go to **Access Control (IAM)**
3. Click **+ Add** → **Add role assignment**
4. Select **Storage Blob Data Contributor**
5. Assign to: **Synapse Workspace Managed Identity**
6. Save

**Verification:**
```python
# Test access
test_path = "abfss://fintech@fintechdatagdsde.dfs.core.windows.net/"
mssparkutils.fs.ls(test_path)
```

---

### Issue #6: Delta Table Corrupted After Failed Write

**Symptoms:**
- Could read Delta table yesterday
- Today getting "corrupted checkpoint" errors
- `_delta_log` folder has incomplete transaction logs

**Root Cause:**
Notebook failure during write operation left incomplete transaction in Delta log.

**Solution:**

```python
# Option 1: Repair Delta table
from delta.tables import DeltaTable

delta_table = DeltaTable.forPath(spark, table_path)
delta_table.vacuum(0)  # Clean up old files

# Option 2: Overwrite completely
df.write.format("delta").mode("overwrite").option("overwriteSchema", "true").save(table_path)
```

---

### Issue #7: Performance Degradation with Large Datasets

**Symptoms:**
- Bronze to Silver transformation taking 20+ minutes
- Out of memory errors on large tables (Transactions: 100K+ rows)

**Solution:**

```python
# Partition large tables
df.write \
  .format("delta") \
  .partitionBy("transaction_date") \
  .mode("overwrite") \
  .save(path)

# Optimize file sizes
spark.sql(f"OPTIMIZE delta.`{path}`")

# Z-order for frequently filtered columns
spark.sql(f"OPTIMIZE delta.`{path}` ZORDER BY (customer_id, transaction_date)")

# Adjust Spark configuration
spark.conf.set("spark.sql.shuffle.partitions", "200")
spark.conf.set("spark.sql.files.maxPartitionBytes", "134217728")  # 128MB
```

---

## 📊 Monitoring & Observability

### Pipeline Monitoring

**Synapse Studio → Monitor → Pipeline runs**

View metrics:
- ✅ Pipeline run status (Succeeded/Failed/In Progress)
- ⏱️ Duration per activity
- 📊 Data copied (rows, size)
- 🔄 Parallel execution visualization
- 📝 Detailed error messages

### Spark Job Monitoring

**Synapse Studio → Monitor → Apache Spark applications**

Track:
- Spark job stages and tasks
- Executor metrics (CPU, memory, I/O)
- DAG visualization
- Job duration and bottlenecks

### Data Quality Monitoring

```python
# Add to transformation notebooks
def log_data_quality(df, table_name):
    total_rows = df.count()
    null_counts = {col: df.where(F.col(col).isNull()).count() 
                   for col in df.columns}
    
    log_message(f"""
    Data Quality Report - {table_name}
    ================================
    Total Rows: {total_rows}
    Null Counts: {null_counts}
    Duplicate Rows: {df.count() - df.dropDuplicates().count()}
    """)
```

### Delta Lake Auditing

```python
# View Delta table history
from delta.tables import DeltaTable

delta_table = DeltaTable.forPath(spark, table_path)
delta_table.history().show()

# Time travel to previous version
df = spark.read.format("delta").option("versionAsOf", 0).load(table_path)

# Restore to previous version
delta_table.restoreToVersion(0)
```

---

## ⚡ Performance Optimization

### 1. File Compaction

```python
# Compact small files in Delta tables
spark.sql(f"OPTIMIZE delta.`{table_path}`")

# With Z-ordering
spark.sql(f"""
    OPTIMIZE delta.`{table_path}` 
    ZORDER BY (customer_id, transaction_date)
""")
```

### 2. Partitioning Strategy

```python
# Partition by date for time-series data
fact_transactions_df.write \
    .format("delta") \
    .partitionBy("year", "month") \
    .mode("overwrite") \
    .save(f"{gold_path}fact_transactions/")
```

### 3. Caching Frequently Used Data

```python
# Cache dimension tables
dim_customers_df.cache()
dim_customers_df.count()  # Materialize cache
```

### 4. Broadcast Joins for Small Dimensions

```python
from pyspark.sql.functions import broadcast

# Broadcast small dimension tables
fact_df = large_fact_df.join(
    broadcast(small_dim_df), 
    "customer_id"
)
```

### 5. Vacuum Old Files

```python
# Clean up old files (older than 7 days)
spark.sql(f"VACUUM delta.`{table_path}` RETAIN 168 HOURS")
```

---

## 🔮 Future Enhancements

### Phase 2 Roadmap

- [ ] **Incremental Loading**
  - Implement change data capture (CDC)
  - Watermark-based incremental processing
  - Merge operations instead of full overwrites

- [ ] **Data Governance**
  - Microsoft Purview integration
  - Data lineage tracking
  - Sensitive data classification

- [ ] **Advanced Analytics**
  - Customer segmentation (RFM analysis)
  - Churn prediction models
  - Loan default risk scoring

- [ ] **Real-time Streaming**
  - Azure Event Hubs integration
  - Spark Structured Streaming
  - Real-time dashboards

- [ ] **Machine Learning Integration**
  - Azure ML integration
  - Automated model training pipelines
  - Feature engineering automation

- [ ] **Enhanced Monitoring**
  - Azure Monitor integration
  - Custom metrics and alerts
  - Cost tracking and optimization

### Phase 3 Ideas

- Implement data mesh architecture
- Multi-region replication
- Self-service analytics platform
- Advanced security with column-level encryption

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

### How to Contribute

1. **Fork the Repository**
   ```bash
   git clone https://github.com/mtousif2303/fintech-azure-data-lakehouse.git
   cd fintech-azure-data-lakehouse
   git checkout -b feature/your-feature-name
   ```

2. **Make Your Changes**
   - Follow PEP 8 style guide for Python code
   - Add comments and docstrings
   - Update documentation as needed

3. **Test Your Changes**
   - Test locally before submitting
   - Ensure no breaking changes
   - Add unit tests if applicable

4. **Submit Pull Request**
   ```bash
   git add .
   git commit -m "Add: your feature description"
   git push origin feature/your-feature-name
   ```
   
   Then create a PR on GitHub with:
   - Clear description of changes
   - Screenshots (if UI changes)
   - Link to related issues

### Contribution Areas

- 🐛 Bug fixes
- ✨ New features
- 📝 Documentation improvements
- ⚡ Performance optimizations
- 🧪 Test coverage
- 🎨 Code refactoring

---

## 📄 License

This project is licensed under the **MIT License**.

```
MIT License

Copyright (c) 2024 Mohammed Tousif

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 👤 Author

**Mohammed Tousif**

🔗 **Connect with me:**

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/mtousif2303)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mohamedtousif/)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:mtousif03012026@gmail.com)

📧 **Email:** mtousif03012026@gmail.com  
💼 **LinkedIn:** [Mohammed Tousif](https://www.linkedin.com/in/mohamedtousif/)  
🐙 **GitHub:** [@mtousif2303](https://github.com/mtousif2303)

---

## 🙏 Acknowledgments

Special thanks to:

- **Microsoft Azure Team** - For comprehensive Synapse Analytics documentation
- **Delta Lake Community** - For excellent Delta Lake resources and best practices
- **Databricks** - For pioneering the Medallion Architecture pattern
- **Apache Spark Community** - For PySpark documentation and examples

### Resources Used

- [Azure Synapse Analytics Documentation](https://docs.microsoft.com/en-us/azure/synapse-analytics/)
- [Delta Lake Documentation](https://docs.delta.io/)
- [Medallion Architecture Guide](https://www.databricks.com/glossary/medallion-architecture)
- [PySpark API Reference](https://spark.apache.org/docs/latest/api/python/)

---

If you encounter any issues or have questions:

1. **Check Documentation:** Review the `/docs` folder
2. **Search Issues:** Look for similar problems in [GitHub Issues](https://github.com/mtousif2303/fintech-azure-data-lakehouse/issues)
3. **Create New Issue:** If not found, [open a new issue](https://github.com/mtousif2303/fintech-azure-data-lakehouse/issues/new)
4. **Contact:** Reach out via email or LinkedIn

---

## 📚 Additional Resources

### Learn More

- **Azure Synapse Best Practices:** [Link](https://docs.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices)
- **Delta Lake Optimization:** [Link](https://docs.delta.io/latest/optimizations-oss.html)
- **Data Engineering on Azure:** [Microsoft Learn Path](https://learn.microsoft.com/en-us/training/paths/data-engineering-with-azure-databricks/)

---

<div align="center">

**⭐ If you find this project helpful, please consider giving it a star! ⭐**

**Made with ❤️ by Mohammed Tousif**

</div>
