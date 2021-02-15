# AWS RDS - Relational Database Service

- It is a managed database service for relational databases
- It allows us to create databases in the cloud that are managed by AWS
- RDS offerings provided by AWS:
    - PostreSQL
    - MySQL
    - MariaDB
    - Oracle
    - Microsoft SQL Server
    - Aurora
- Advantages of AWS RDS over deploying an relational database on EC2:
    - RDS is a managed service, meaning:
        - Automated provisioning, OS patching
        - Continuous backups and restore to specific timestamp (Point in Time Restore)
        - Monitoring dashboards
        - Read replicas
        - Multi AZ setup
        - Maintenance windows for upgrades
        - Scaling capability (vertical and horizontal)
        - Storage backed by EBS (GP2 or IO)
- Disadvantages:
    - No SSH into the instance which hosts the database

## RDS Backups

- Backups are automatically enabled in RDS
- AWS RDS provides automated backups:
    - Daily fill backup of the database (during the maintenance window)
    - Transaction logs are backed-up by RDS every 5 minutes which provides the ability to do point in time restores
    - There is a 7 day retention for the backups which can be increased to 35 days
- DB Snapshots:
    - There are manually triggered backups by the users
    - Retention can be as long as the user wants
    - Helpful for retaining the state of the database for longer period of time

## RDS Read Replicas

- Read replicas helps to scale the read operations
- We can create up to 5 read replicas
- These replicas can be within AZ, cross AZ or in different regions
- The data between the main database and the read replicas is replicated **asynchronously** => reads are eventually consistent
- Read replicas can be promoted into their own database
- Use case for read replicas:
    - Production database is up and running taking on normal load
    - There is a new feature for running some reporting for analytics which may cause slow downs and may overload the database
    - To fix this we can create read replicas for reporting
- Read replicas are used for SELECT operations (not INSERT, UPDATE, DELETE)
- Network cost for read replicas:
    - In AWS there is network cost if data goes from one AZ to another
    - In case of cross AZ replication, additional costs may incur because of network traffic
    - To reduce costs, we could have the read replicas in the same AZ

## RDS Multi AZ (Disaster Recovery)

- RDS Multi AZ replication is done using **synchronous** replication
- In case of multi AZ configuration we get one DNS name
- In case of the main database goes down, the traffic is automatically re-routed to the failover database
- Multi AZ is not used for scaling
- The read replicas can be set up as Multi AZ for Disaster Recovery

## RDS Security

### Encryption

- AWS RDS provides rest encryption: possibility to encrypt the master and read replicas with AWS KMS - AES-256 encryption
    - Encryption has to be defined at the launch time
    - **If the master is not encrypted, the read replicas cannot be encrypted**
    - Transparent Data Encryption (TDE) is available for Oracle and SQL Server
- In-flight encryption: uses SSL certificates to encrypt data from client to RDS in flight
    - It is required SSL a trust certificate when connecting to database
    - To enforce SSL:
        - PostgeSQL: rds.force_ssl=1 in the AWS RDS Console (Parameter Groups)
        - MySQL: `GRANT USAGE ON *.* To 'user'@'%' REQUIRE SSL;`
- Encrypting RDS backups:
    - Snapshots of un-encrypted RDS databases are un-encrypted
    - Snapshots of encrypted RDS databases are encrypted
    - We can copy an un-encrypted snapshot into an encrypted one
- Encrypt an un-encrypted RDS database:
    - Create a snapshot
    - Copy the snapshot and enable encryption for the snapshot
    - Restore the database from the encrypted snapshot
    - Migrate application from the old database to the new one and delete the old database

### Network Security and IAM

- Network security:
    - RDS databases are usually deployed within a private subnet
    - RDS security works by leveraging security groups (similar to EC2), they control who can communicate with the database instance
- Access management:
    - There are IAM policies which help control who can manage an AWS RDS database (through the RDS API)
    - Traditional username/password can be used to login into the database
    - IAM-based authentication can be used to login into MySQL and PostgreSQL 
- IAM authentication:
    - IAM database authentication works with MySQL and PostgreSQL
    - We don't need a password to authenticate, just an authentication token obtained through IAM and RDS API calls
    - The token has a lifetime of 15 minutes
    - Benefits:
        - Network in/out must be encrypted using SSL
        - IAM is used to centrally manage users instead of DB credentials
        - We can manage IAM roles and EC2 instance profiles for easy integration

### Security Summary

- Encryption at rest:
    - It is done only when the database is created
    - To encrypt an existing database, we have create a snapshot, copy it as encrypted, and create an encrypted database from the snapshot
- Our responsibility:
    - Check the ports/IP/security groups inbound rules
    - Take care of database user creation and permissions or manage them through IAM
    - Create a database with or without public access
    - Ensure parameter groups or DB is configured to only allow SSL connections
- AWS responsibility:
    - DB patching
    - Underlying OS patching and updates


