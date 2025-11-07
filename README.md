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
