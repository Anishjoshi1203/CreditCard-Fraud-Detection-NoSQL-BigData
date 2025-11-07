# Credit Card Fraud Detection – NoSQL Big Data Project

## Project Overview
This project demonstrates an **end-to-end Big Data pipeline** designed for **Credit Card Fraud Detection** using the Hadoop ecosystem and NoSQL databases.  
It integrates **data ingestion (Sqoop)**, **data transformation (Hive, HBase)**, and **real-time streaming analysis (Kafka, Spark Streaming)** deployed on **AWS EMR**.

The goal is to detect potential fraudulent credit card transactions by applying business rules and verifying transactions against control limits, credit scores, and location consistency.

---

##  Architecture
The project follows a hybrid **Batch + Streaming** architecture:

## 🔹 Workflow Summary
1. **Data Ingestion:** Historical data and member information are imported from **AWS RDS** into **HDFS** using **Apache Sqoop**.
2. **Data Transformation:** Hive external and ORC tables are created for optimized storage and querying.
3. **Hive-HBase Integration:** Hive tables are integrated with **HBase** to serve as a NoSQL lookup system.
4. **Lookup & Rule Validation:** Lookup tables store key transaction limits, credit scores, and zip codes for quick validation.
5. **Streaming Layer:** Real-time transactions flow from **Kafka** into **Spark Streaming**, where fraud detection rules are applied dynamically.
6. **Output:** Classified transactions are written back to the **HBase NoSQL database** for analytics and reporting.

---

## ⚙️ Technologies Used

| Layer | Tools & Technologies |
|-------|----------------------|
| **Data Source** | AWS RDS (MySQL) |
| **Ingestion** | Apache Sqoop |
| **Storage** | HDFS, Hive |
| **NoSQL Database** | HBase |
| **Processing** | Apache Spark (Batch & Streaming) |
| **Streaming** | Apache Kafka |
| **Deployment** | AWS EMR Cluster |
| **Scripting** | Python (UDFs, Kafka Consumers) |

---

## Project Components

###  1. Sqoop Data Ingestion (`Sqoop Data Ingestion.pdf`)
- Imported data from **AWS RDS** (`card_member`, `member_score` tables) into **HDFS** using Sqoop.
- Verified imports via HDFS commands.
- Example command:
  ```bash
  sqoop import \
  --connect jdbc:mysql://<aws-rds-endpoint>/cred_financials_data \
  --username upgraduser --password upgraduser \
  --table card_member --target-dir /user/CCFD_project/card_member -m 1

 ### 2. Load NoSQL (LoadNoSQL.pdf)
Created Hive external and ORC tables for credit card transactions.
Integrated Hive with HBase to build the card_transactions_hbase table.
Inserted and validated records in HBase with timestamp conversions.

### 3. Create NoSQL (Create NoSQL.pdf)
Created the Lookup Table in Hive-HBase integration:
CREATE TABLE LOOKUP_DATA_HBASE(
    CARD_ID STRING,
    UCL DOUBLE,
    SCORE INT,
    POSTCODE STRING,
    TRANSACTION_DT TIMESTAMP
)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
    "hbase.columns.mapping"=":key, lookup_card_family:ucl,
     lookup_card_family:score,
     lookup_transaction_family:postcode,
     lookup_transaction_family:transaction_dt"
)
TBLPROPERTIES ("hbase.table.name" = "lookup_data_hive");

 ### 4.Pre-Analysis (PreAnalysis.pdf)
Performed joins and filtering to populate the Lookup Table with the latest transaction and customer details.
Validated with:
SELECT COUNT(*) FROM LOOKUP_DATA_HBASE;
SCAN 'LOOKUP_DATA_HIVE';

### 5. Scripts Execution (Scripts Execution.pdf)
Created and validated multiple components on AWS EMR:
External → ORC → Hive-HBase integrated tables
Lookup table creation
Sqoop imports for RDS tables
Ranking & UCL logic for transactions
End-to-end validation of all tables in HBase

 ### 6. Streaming & Logic Implementation (Logic Final.pdf)
Configured Kafka Producer and Consumer.
Implemented custom Python UDFs for rule-based validation:
verify_ucl_data: Check if transaction amount < UCL.
verify_credit_score_data: Ensure credit score > 200.
verify_postcode_data: Validate distance between zip codes.
calculate_speed: Compute transaction velocity.
verify_rules_status: Combine all rules for fraud detection.
Integrated Spark Streaming with Kafka topic:
spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.4.5 driver.py


 ## Results & Learnings

Designed a robust, scalable fraud detection system capable of handling both historical and live transaction data.
Learned key integrations across the Hadoop ecosystem: Sqoop → Hive → HBase → Spark Streaming.
Understood the challenges of streaming ingestion, NoSQL design, and real-time validation.
