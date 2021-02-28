# Databases

## Choosing the Right Database

- There are several databases on AWS from which we can choose from
- Guid line questions to be able to select the best database for the product:
    - Is the workload read-heavy, write-heavy or balanced? What are the throughput needs? Will the throughput change, will fluctuate or will we have to scale the DB over time?
    - How much data do we store and for how long? Will the size of the data grow? What is the average size of an object in the DB? How frequently are these objects accessed?
    - Data durability: should the data be stored for a week or forever? Is the database going to be a source of truth?
    - What are the latency concerns?
    - What is the data model? How will the data be queried? Will the data be structured, semi-structured or unstructured?
    - Do we need strong schema or flexible schema? Do we need reporting? Do we need advanced search?
    - What are the license costs? Can we switch to a cloud native database such as Aurora, DynamoDB, etc?

## Database Types on AWS

- RDBMS (SQL/OLTP): RDS, Aurora - great for joins and normalized data
- NoSQL: DynamoDB, ElastiCache (key/value pairs), Neptune (graphs), DocumentDB (json) - no joins, no SQL
- Object Store: S3 (for big objects) / Glacier (for backups, archive)
- Data Warehouse (SQL Analytics / BI): Redshift (OLAP), Athena
- Search: ElasticSearch (JSON) - free text, unstructured searches
- Graphs: Neptune - displays relationships between data

## RDS

- Managed PostgreSQL, MySQL, Oracle, SQL Server
- AWS provisions an EC2 instance behind the scenes and EBS Volume
- RDS has support for multi AZ deployment and read replicas
- Security through IAM, security groups, KMS, SSL
- Backup/Snapshot/Point in time restore functionality
- Managed and scheduled maintenance
- Monitoring happens through CloudWatch
- Use cases: store relational datasets, perform SQL queries, transactional inserts, deletes, updates

### RDS for Solutions Architects

- Operations: small downtime when a failover happens, when maintenance happens, when scaling of read replicas. Restoring snapshots implies manual intervention
- Security: AWS is responsible for OS security. We are responsible for setting up KMS, SG, IAM policies, authorizing users, using SSL
- Reliability: Multi AZ feature, failover in case of failures
- Performance: depends on the EC2 type, EBS volume type. We have the ability to add Read Replicas. RDS does not auto scale
- Cost: pay per hour based on provisioned EC2 and EBS

## Aurora

- Managed by AWS, under the RDS service, having a compatible API with PostgreSQL and MySQL
- Data is held in 6 replicas across 3 AZs
- It has some auto healing capabilities
- Auto scaling read replicas which can be global
- Aurora databases can be global which can be used for disaster recovery purposes or latency purposes
- Auto scaling of storage form 10GB to 64TB
- We define an EC2 instance for Aurora instances
- Aurora has the same security, monitoring and maintenance features as other RDS databases
- Aurora has a serverless option
- Use cases: similar to RDS, but with less maintenance, more flexibly, more performant at a higher cost

### Aurora for Solutions Architects

- Operations: less operations, auto scaling for storage
- Security: AWS is responsible for OS security. We are responsible for setting up KMS, SG, IAM policies, authorizing users, using SSL
- Reliability: Multi Az, highly available, Aurora serverless option
- Performance: 5x performance (according to AWS) compared to other RDS types. up to 15 read replicas (only 5 for RDS)
- Cost: pay per hour based on EC2 and storage usage. Possibly lower costs compared to other enterprise grade databases such as Oracle

## ElastiCache

- Managed Redis or Memcached
- In-memory data store, sub-millisecond latency
- Must provision and EC2 instance type
- Offers support for clustering (Redis) and Multi AZ, read replicas (sharding)
- Security through IAM, SG, KMS, Redis Auth
- Possibility to do backups, snapshots and point in time restore (for Redis)
- It has managed and scheduled maintenance
- Monitoring happens through CloudWatch
- Uses cases: key/value store, frequent reads, less writes, cache results for DB queries, store session data for websites

### ElastiCache for Solutions Architects

- Operations: same as RDS
- Security: same as RDS without the possibility of IAM policies. In the other hand there is Redis Auth for authentication
- Reliability: clustering, multi AZ
- Performance: sub-millisecond performance, read replicas for sharding, popular cache option
- Cost: pay per hour based on EC2 instance type and storage

## DynamoDB

- Managed NoSQL database, proprietary to AWS
- Serverless, provisioned capacity, supports auto scaling, provides on demand capacity as well
- Can replace ElastiCache as a key/value store
- Highly available, Multi AZ by default, reads and writes are decoupled, DAX for read cache
- Reads can be eventually consistent or strongly consistent
- Security, authentication and authorization is done through IAM
- We can enable DynamoDB Streams to stream all the changes in DynamoDB and integrate with AWS Lambda
- Provides backup/restore features, Global Table feature
- Monitoring is done through CloudWatch
- The tables can only be queried on primary key, sort key or indexes
- Use cases: serverless application development, distributed serverless cache

### DynamoDB for Solutions Architects

- Operations: no operations needed, auto scaling capability out of the box, serverless
- Security: full security through IAM policies, KMS encryption, SSL in flight
- Reliability: Multi AZ, provides backups
- Performance: single digit millisecond performance, DAX for caching reads, performance does not degrade if the application scales
- Cost: pay per provisioned capacity and storage usage (no need to guess in advance any capacity)

## S3

- S3 is a key/value store for objects
- Great for big objects, not so great for small objects
- Serverless, scales infinitely, max object size is 5TB
- Eventually consistency for overwrites and deletes, strong consistency for new objects
- Tiers: S3 Standard, S3 IA, S3 One Zone IA, Glacier
- Features: versioning, encryption, cross region replications
- Security: IAM, bucket policies, ACL
- Encryption: SSE-S3, SSE-KMS, SSE-C, client side encryption, SSL in transit
- Use cases: static files, key value store for big files, website hosting

### S3 for Solutions Architects

- Operations: no operations needed
- Security: IAM, bucket policies, ACL, encryption, SSL
- Reliability: 99.999999999% durability / 99.99% availability, multi AZ, cross-region replication
- Performance: scales to thousands of reads/writes per second, transfer acceleration / multi-part upload for big files
- Cost: pay per storage usage, network cost, request number

## Athena

- Fully serverless database (query engine) with SQL capabilities
- Used to query data in S3
- Pay per query
- Output results back to S3
- Secured through IAM
- Use cases: one time SQL queries, serverless queries on S3, log analytics

### Athena for Solutions Architects

- Operations: no operations needed, serverless
- Security: IAM + S3 security
- Reliability: managed service, uses Presto engine, highly available
- Performance: queries scale based on data size
- Cost: per per query / per TB of data scanned, serverless

## Redshift

- Redshift is based on PostgreSQL, but it's not used for OLTP
- It's **OLAP - only analytical processing (analytics and data warehousing)**
- 10x better performance than other data warehouses, scales to PBs of data
- Columnar database: data is stored in columns
- Massively parallel query execution (MPP), highly available
- Pay as you go based on the instances provisioned
- Has a SQL interface for performing the queries
- Great to use with BI tools such as AWS Quicksight or Tableau
- Data is loaded form S3, DynamoDB, Database Migration Service (DMS), other DBs
- Can scale from 1 node up to 128 nodes, up to 160 GB of space per node
- Node types: compute nodes for performing queries, result is sent to leader nodes
- Redshift Spectrum: perform queries directly against S3 (no need to load the data)
- Provides backup and restore
- Security is accomplished with VPC/IAM/KMS
- Redshift Enhanced VPC Routing: COPY/UNLOAD goes through VPC

### Snapshots and Disaster Recovery

- Snapshots are point-in-time backups of a cluster, stored internally in S3
- Snapshots are incremental (only what has changed is saved)
- Snapshots can be restored into new Redshift clusters
- Snapshots can be automated: every 8 hours, every 5 GB, or on schedule
- Manual snapshots: snapshots are retained until we delete them
- We can configure Redshift to automatically copy snapshots to another regions

### Redshift Spectrum

- Data can be queried from S3 without the need to load it into Redshift
- We must have a Redshift cluster available to start the query
- The query is then submitted to thousands of Redshift Spectrum nodes

### Redshift for Solutions Architects

- Operations: similar to RDS
- Security: IAM, VPC, KMS, SSL
- Reliability: highly available, auto healing feature
- Performance: 10x performance vs other data warehousing, compression
- Cost: pay per node provisioned, 1/10th of the cost of other warehouses

## Neptune

- Fully managed graph database
- Graph use cases:
    - Highly relational data
    - Social networking
    - Knowledge graphs
- Highly available across 3 AZ, up to 15 read replicas
- Provides point-in-time recovery, continuous back-up to S3
- Support for KMS encryption at rest + HTTPS

### Neptune for Solutions Architects

- Operations: similar to RDS
- Security: IAM, VPC, KMS, SSL, IAM Authentication
- Reliability: Multi AZ, clustering
- Performance: best suited for graphs, clustering can improve performance
- Cost: similar for RDS, per per node provisioned

## ElasticSearch

- Open source technology
- With ElasticSearch we can search any field, we can have partial matches as well
- It's common to use ElasticSearch as a complement to another database
- We can provision a cluster of instances
- Built-in integration with: Amazon Kinesis Data Firehose, AWS IoT and Amazon CloudWatch Logs for data ingestion
- Security through Cognito and IAM, KMS encryption, SSL and VPC
- Comes with Kibana and Logstahs - ELK stack

### ElasticSearch for Solutions Architects

- Operations: similar to RDS
- Security: Cognito, IAM, VPC, KMS, SSL
- Reliability: Multi AZ, clustering
- Performance: based on ElasticSearch project, petabyte scale
- Cost: similar for RDS, per per node provisioned