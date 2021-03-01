# DMS - Database Migration Service

- Quickly and securely migrate databases to AWS
- It is resilient and self healing
- The source database remains available during migration
- Supports:
    - Homogeneous migrations, example Oracle to Oracle
    - Heterogeneous migrations, example: Microsoft SQL Server to Aurora
- Supports continuous data replication using CDC
- We must create an EC2 instance to perform the replication tasks

## DMS Sources and Targets

- Sources:
    - On-premise and EC2 instance databases: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, MongoDB, SAP, DB2
    - Azure SQL Database
    - Amazon RDS: all including Aurora
    - Amazon S3
- Targets:
    - On-premise and EC2 instance databases: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, AP
    - Amazon RDS
    - Redshift
    - DynamoDB
    - S3
    - ElasticSearch
    - Kinesis Data Streams
    - DocumentDB

## AWS Schema Conversion Tool (SCT)

- Converts a database schema form one engine to another. Example: OLTP (SQL Server, Oracle) to MySQL, PostgreSQL, Aurora; OLAP (Teradata or Oracle) to Redshift
- We do not need to use SCT if we are migration from the same DB engine to the same DB engine