# Enterprise-Grade Data Ingestion Pipeline on AWS

## 📄 Project Overview

This project implements a scalable, secure, and automated data ingestion and quality assurance pipeline using AWS services. It handles daily `.dat` file ingestion from multiple entities, performing validation, transformation, schema enforcement, and data quality checks before storing data into Iceberg tables for analytics. The system supports both Day 0 and Delta loads with built-in support for CDC and SCD Type 2.

---

## 🧭 Architecture Overview

1. **Ingestion & Triggering**
   - `.dat` files land in the **S3 raw bucket** (per entity, daily)
   - **SQS** receives file event → triggers **Lambda**
   - Lambda performs:
     - Folder creation by entity/date
     - Header/footer and zero-KB validation
     - Metadata logging to **DynamoDB** tables: `FileLog`, `JobFeed`, `BadData`

2. **Processing**
   - Lambda triggers **AWS Step Function**
   - Step Function runs **AWS Glue** job to:
     - Perform **schema validation**
     - Run **data quality checks**
     - Handle **CDC & SCD2** logic
     - Output clean **Parquet** files to **Iceberg tables** in the S3 base bucket (partitioned by date)

3. **Data Quality Framework (DQF)**
   - QA flow: **QA SQS → QA Lambda → QA Step Function → QA Glue**
   - Executes:
     - **Day 0 completeness**
     - **Delta validation**
     - **Cross-bucket DQ comparisons** (raw vs base)
   - Invalid data saved in S3 + logged to DynamoDB `BadData` table

---

## 🛡️ DevOps & Governance

- **Infrastructure as Code**: All AWS resources provisioned using **CloudFormation**
- **CI/CD**: Jenkins pipelines per environment (dev, QA, prod)
- **Security**:
  - **KMS**-encrypted data at rest
  - IAM roles with least privilege
  - **Cross-account roles** for:
    - On-premise data push to S3 raw bucket
    - Athena query access
- **Code Quality & Security**:
  - **SonarQube** for static code analysis
  - **Veracode** for security scanning
- **Testing**: Unit-tested Lambda and Glue scripts

---

## 🔧 Tech Stack

- AWS S3, Lambda, Step Functions, Glue, DynamoDB, SQS, IAM, KMS
- Iceberg (S3 + Parquet format)
- Jenkins, CloudFormation, SonarQube, Veracode
- Python (Lambda/Glue scripting)

---

## 📌 Use Cases

- Enterprise-grade ingestion and transformation pipeline
- Automated schema validation and data quality enforcement
- Cloud-native CDC and SCD2 processing
- Secure and auditable data lake operations

