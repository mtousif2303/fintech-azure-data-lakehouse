# Fintech Azure Data Lakehouse

Tech Stack : Python, Azure SQL Database, SQL, Azure Synapse, ADLS, PySpark, Delta Tables


A complete data engineering solution for migrating and transforming fintech SQL data using Azure cloud services with medallion architecture (Bronze-Silver-Gold layers).

## 📋 Project Overview

This project implements an end-to-end data pipeline that migrates historical data from SQL Server to Azure Data Lake Storage (ADLS) using a medallion architecture pattern. The solution leverages Azure Synapse Analytics and PySpark for data transformation and orchestration.

## 🏗️ Architecture
```
SQL Database → Azure Synapse Pipeline → Bronze Layer (ADLS)
                                            ↓
                                    PySpark Transformation
                                            ↓
                                    Silver Layer (Delta Tables)
                                            ↓
                                    PySpark Transformation
                                            ↓
                                    Gold Layer (Delta Tables)
```

## 🛠️ Tech Stack

- **Language:** Python, SQL, PySpark
- **Cloud Platform:** Microsoft Azure
- **Services:**
  - Azure SQL Database
  - Azure Synapse Analytics
  - Azure Data Lake Storage (ADLS Gen2)
  - Delta Lake
- **Framework:** PySpark
- **Storage Format:** Delta Tables (Parquet-based)

## 📁 Project Structure
```
fintech-azure-data-lakehouse/
├── sql/
│   ├── create_tables.sql          # SQL scripts to create source tables
│   └── insert_sample_data.sql     # Sample historical data insertion
├── synapse/
│   ├── pipelines/
│   │   ├── bronze_ingestion.json  # Bronze layer ingestion pipeline
│   │   └── master_pipeline.json   # Orchestration pipeline
│   └── datasets/
│       └── sql_dataset.json       # Source SQL dataset configuration
├── notebooks/
│   ├── bronze_to_silver.ipynb     # Silver layer transformation
│   └── silver_to_gold.ipynb       # Gold layer transformation
├── config/
│   └── pipeline_config.json       # Configuration parameters
├── docs/
│   ├── setup_guide.md             # Step-by-step setup instructions
│   └── architecture.md            # Detailed architecture documentation
├── .gitignore
├── README.md
└── requirements.txt
```

## 🚀 Features

- **Dynamic Data Ingestion:** Automated pipeline to extract data from SQL Database
- **Medallion Architecture:** Three-tier data processing (Bronze → Silver → Gold)
- **Delta Lake Integration:** ACID transactions and time travel capabilities
- **Scalable Processing:** PySpark for distributed data transformation
- **Automated Orchestration:** Sequential pipeline execution
- **Data Quality:** Progressive data refinement across layers

## 📊 Data Layers

### Bronze Layer (Raw Data)
- Raw data ingested from SQL Database
- Stored in original format with minimal transformation
- Maintains data lineage and audit trail

### Silver Layer (Cleaned Data)
- Cleansed and conformed data
- Data validation and quality checks
- Stored as Delta Tables for efficient querying

### Gold Layer (Business-Ready Data)
- Aggregated and business-ready datasets
- Optimized for analytics and reporting
- Denormalized for performance

## 🔧 Prerequisites

- Azure Subscription
- Azure SQL Database instance
- Azure Synapse Analytics workspace
- Azure Data Lake Storage Gen2 account
- Python 3.8+
- Azure CLI (for deployment)

## 📦 Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/mtousif2303/fintech-azure-data-lakehouse.git
cd fintech-azure-data-lakehouse
```

### 2. Prepare SQL Database
```bash
# Connect to your Azure SQL Database
# Execute table creation scripts
sqlcmd -S your-server.database.windows.net -d your-database -U username -i sql/create_tables.sql
sqlcmd -S your-server.database.windows.net -d your-database -U username -i sql/insert_sample_data.sql
```

### 3. Configure Azure Services
- Create Azure Synapse workspace
- Set up linked services for SQL Database and ADLS
- Configure access permissions and managed identities

### 4. Deploy Pipelines
- Import pipeline JSON files into Synapse Studio
- Configure pipeline parameters
- Set up triggers for automation

### 5. Upload Notebooks
- Upload PySpark notebooks to Synapse workspace
- Configure Spark pool settings
- Test notebooks individually before orchestration

## 🎯 Usage

### Manual Execution
1. Navigate to Azure Synapse Studio
2. Go to Integrate → Pipelines
3. Select `master_pipeline`
4. Click "Add trigger" → "Trigger now"

### Automated Execution
- Configure schedule trigger in Synapse pipeline
- Set execution frequency (daily/hourly/custom)
- Monitor pipeline runs in Monitor hub

## 📈 Pipeline Workflow

1. **Bronze Ingestion Pipeline**
   - Connects to SQL Database
   - Dynamically detects tables
   - Copies data to ADLS Bronze layer

2. **Bronze to Silver Transformation**
   - Reads raw data from Bronze layer
   - Applies data cleansing rules
   - Writes Delta tables to Silver layer

3. **Silver to Gold Transformation**
   - Reads cleaned data from Silver layer
   - Applies business logic and aggregations
   - Writes optimized Delta tables to Gold layer

## 🔍 Monitoring & Logging

- Pipeline execution logs in Synapse Monitor
- Spark job metrics and logs
- Delta table history and time travel queries

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👤 Author

**Mohammed Tousif**
- GitHub: [@mtousif2303](https://github.com/mtousif2303)
- LinkedIn: [Mohammed Tousif](https://www.linkedin.com/in/mohamedtousif/)

## 🙏 Acknowledgments

- Azure Synapse Analytics Documentation
- Delta Lake Community
- Databricks Medallion Architecture Best Practices

## 📞 Support

For issues and questions, please open an issue in this repository.

---

**Note:** Replace placeholder values (server names, credentials) with your actual configuration details before deployment.
