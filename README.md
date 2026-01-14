# Data_Engineering_Assessment

Healthcare Eligibility Ingestion Platform
Enterprise Configuration-Driven Data Pipeline

1. Executive Summary
This document describes the design, implementation, and operation of the Healthcare Eligibility Ingestion Platform, a scalable, configuration-driven PySpark solution for ingesting eligibility data from multiple healthcare partners with heterogeneous file formats.
The platform standardizes partner-provided eligibility data into a single canonical schema, enabling consistent downstream consumption across analytics, reporting, and operational systems.
The solution is designed to meet enterprise data engineering standards, including:
•	Configuration-only onboarding
•	Data quality validation
•	Observability through logging and metrics
•	Testability and maintainability
•	Cloud-agnostic execution (Databricks / Google Colab)

2. Business Problem Statement
Healthcare organizations routinely receive eligibility data from multiple external partners. These partner feeds vary across:
•	File formats (CSV, pipe-delimited, custom delimiters)
•	Column naming conventions
•	Date formats
•	Data quality standards
Without a standardized ingestion framework, onboarding new partners requires:
•	Custom code changes
•	Increased operational risk
•	Slower time-to-market

3. Solution Objectives
The primary objectives of this platform are:
1.	Standardization
              Normalize heterogeneous partner data into a single enterprise schema.
2.	Scalability
               Enable onboarding of new partners without modifying ingestion logic.
3.	Reliability
               Enforce data quality rules and prevent invalid data propagation.
4.	Observability
               Provide metrics and logs for operational monitoring.
5.	Maintainability
               Ensure that the solution is testable, readable, and extensible.

4. High-Level Architecture
4.1 Logical Flow
Partner Files
    ↓
Partner Configuration
    ↓
Generic Spark Ingestion Layer
    ↓
Standardization & Validation Layer
    ↓
Metrics & Logging
    ↓
Unified Eligibility Dataset


4.2 Architectural Principles
Principle	Description
Configuration-Driven	Partner differences are handled via metadata, not code
Spark-Native	Uses Spark SQL expressions for scalability
Stateless Processing	Each run is independent and repeatable
Cloud-Agnostic	Runs on Databricks or open-source Spark

5. Canonical Data Model
The platform outputs a standardized eligibility dataset with the following schema:
Field Name	Description
external_id	Partner-provided unique member identifier
first_name	Member first name (Title Case)
last_name	Member last name (Title Case)
dob	Date of birth (ISO-8601 format)
email	Normalized lowercase email
phone	Standardized phone number (XXX-XXX-XXXX)
partner_code	Internal identifier for the source partner
This schema is partner-agnostic and suitable for enterprise downstream systems.

6. Configuration Management
6.1 Partner Configuration Model
Each partner is defined through a configuration object specifying:
•	File delimiter
•	Source-to-target column mapping
•	Date format
•	Partner identifier
Example:
{
  "partner_code": "ACME",
  "delimiter": "|",
  "column_mapping": {...},
  "date_format": "MM/dd/yyyy"
}

6.2 Governance Benefits
•	Eliminates code branching per partner
•	Simplifies change management
•	Reduces regression risk
•	Enables non-engineering configuration updates

7. Data Processing Lifecycle
7.1 Ingestion Layer
•	Reads partner files using Spark’s distributed reader
•	Applies delimiter and header settings dynamically
•	Renames partner columns using configuration mappings

7.2 Transformation & Standardization Layer
The transformation layer enforces enterprise data standards:
•	Name capitalization
•	ISO-compliant date formats
•	Email normalization
•	Phone number standardization
All transformations use Spark SQL expressions, avoiding Python UDFs to preserve performance and optimizer benefits.

7.3 Validation & Data Quality Controls
The platform enforces the following controls:
Check	Action
Missing external_id	Record filtered
Invalid date format	Flagged
Invalid phone number	Flagged
Data quality results are logged for audit and monitoring purposes.

8. Observability & Monitoring
8.1 Logging
Structured logs capture:
•	Partner ingestion start/end
•	Record counts
•	Data quality results
•	Pipeline completion status
Logs are compatible with:
•	Databricks logging
•	Cloud monitoring platforms
•	Enterprise log aggregators

8.2 Metrics
Key metrics include:
•	Records processed per partner
•	Invalid records detected
•	Successful pipeline completion
These metrics support operational dashboards and also the SLA tracking.

9. Testing Strategy
9.1 Unit Tests
Unit tests validate:
•	Transformation correctness
•	Required field enforcement
•	Data normalization logic
Tests are executable in:
•	Google Colab
•	Local Spark
•	CI/CD pipelines

9.2 Test Coverage Philosophy
The focus is on:
•	Business-critical transformations
•	Regression prevention
•	Confidence in partner onboarding

10. Deployment & Execution
10.1 Supported Environments
Environment	Supported
Google Colab	yes
Databricks Community	yes
Databricks Enterprise	yes
Open-source Spark	yes

10.2 Execution Model
•	Stateless batch execution
•	Idempotent processing
•	Optional persistence to Parquet or Delta Lake

11. Onboarding a New Partner
To onboard a new partner:
1.	Add a new configuration entry
2.	Define:
a.	Delimiter
b.	Column mapping
c.	Date format
d.	Partner code
3.	Execute the pipeline
No code changes are required.

12. Security & Compliance Considerations
•	No credentials embedded in code
•	PII handled in controlled transformations
•	Schema standardization simplifies downstream masking/encryption
•	Compatible with HIPAA-aligned data handling workflows

13. Conclusion
The Healthcare Eligibility Ingestion Platform provides:
•	A scalable foundation for partner data onboarding
•	Enterprise-grade reliability and observability
•	Clean separation between configuration and logic
•	Long-term maintainability and extensibility


