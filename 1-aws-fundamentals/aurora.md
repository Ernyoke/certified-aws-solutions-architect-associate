# Amazon Aurora

- Aurora is a proprietary technology from AWS (not open sourced)
- It provides support for both PostgreSQL and MySQL drivers in order to be able to connect to it
- Aurora is cloud optimized, it claims 5x performance over MySQL and over 3x performance over PostgreSQL
- Aurora storage automatically grows in increments from 10GB up to 64TB
- Aurora can have 15 replicas (while MySQL has only 5), replication process is faster
- Failover is Aurora is instantaneous
- Aurora costs more than classic RDS (around 20%)
- Aurora availability:
    - Stores 6 copies of the data across 3 AZs - only needs 4 copies for writes and 3 for reads
    - It has self healing properties with peer-to-peer replication
    - Storage is split across 100s of volumes
- In Aurora only one instance is used for writes (master instance)
- In case of master failure, the failover happens in less than 30 seconds
- Provides support for cross region replication

## Aurora DB Cluster

- In Aurora we always have one writer end-point
- We can have up to 15 read replicas
- Read replicas can be inside an auto scaling groups which provides the right read capacity all the time
- Read end-point: basically does load balancing between read replicas

## Aurora Security

- It is similar to basic RDS security, because it is using the same engine under the hood
- Provides encryption at rest using KMS
- Provides automated backups, snapshots and replicas are also encrypted
- Encryption in flight is done using SSL
- Possibility to authenticate using IAM token
- The Aurora instance is protected with security groups (just like basic RDS)
- There is no way to SSH into an Aurora instance

## Aurora Serverless

- Provides automated database instantiation and auto-scaling based on actual usage
- Recommended for infrequent, intermittent or unpredictable workloads
- No capacity planning is needed
- Users pay per seconds, can be cost-effective

## Global Aurora

- There are two ways to have cross region replication:
    - Aurora Cross Region Read Replicas:
        - Useful for disaster recovery
        - Simple to put in place
    - Aurora Global Database (recommended):
        - We can specify one primary region for read/write
        - We can have up to 5 secondary regions (read-only)
        - Replication lag is bellow 1 second
        - We can have up to 16 read replicas per secondary region
        - In case we need to promote one region, the RTO (Recovery Time Objective) is bellow 1 minute