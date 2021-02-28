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

### RDS for Solution Architects

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

### Aurora for Solution Architects

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

### ElastiCache for Solution Architects

- Operations: same as RDS
- Security: same as RDS without the possibility of IAM policies. In the other hand there is Redis Auth for authentication
- Reliability: clustering, multi AZ
- Performance: sub-millisecond performance, read replicas for sharding, popular cache option
- Cost: pay per hour based on EC2 instance type and storage