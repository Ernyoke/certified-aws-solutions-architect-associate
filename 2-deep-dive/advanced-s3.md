# Advanced S3

## S3 MFA-Delete

- To use MFA-Delete we have to enable versioning on the selected bucket
- MFA will be required when:
    - We want to permanently delete an object version
    - We want to suspend the versioning on the bucket
- MFA wont be required when:
    - We want to enable versioning
    - We want to list deleted versions
    - We want to add a delete marker to an object
- **MFA-Delete can be enabled/disabled only by the owner of the bucket (root account)!**
- MFA-Delete currently can only be enabled using the CLI

## S3 Access Logs

- For audit purposes we would want to log all access to S3 buckets
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- The data can be analyzed by some data analysis tools or Amazon Athena

### Warnings

- We should never set our logging bucket to be the monitored bucket! This may create a logging loop causing the bucket to grow exponentially

## S3 Replication

- In order to be able enable replication:
    - We must enable versioning on the source and destination buckets
- There are 2 types of replication:
    - Cross Region Replication (CRR): bucket are in a different region
        - Used for: compliance, lower latency access, replication across accounts
    - Same Region Replication (SRR): buckets are in the same region
        - Used for: log aggregation, live replication between production and test accounts
- In both cases the buckets can be in separate accounts
- Copying between replica buckets happens asynchronously (it is very quick)
- In order to be ably to copy between replicas, an IAM permission has to be assigned to the source bucket

### Replication Notes

- Only the new objects are replicated after the replication is activated (no retroactive replication)
- For DELETE operations:
    - For deletion without version ID, a delete marker is added to he object. Deletion is not replicated
    - For deletion with version ID, the object is deleted in the source bucket. Deletion is not replicated
- There is no replication chaining!

## S3 Pre-singed URLs

- We can generate pre-signed URLs using the SDK and the CLI
- Pre-signed URLs have a default wait time of 3600 seconds. This can be changed with `--expires-in` argument
- Users given a pre-signed URL will inherit the permissions of the person who generated the ULR

## S3 Storage Classes

- Amazon S3 Standard-General Purpose
- Amazon S3 Standard-Infrequent Access (IA)
- Amazon S3 One Zone-Infrequent Access
- Amazon S3 Intelligent Tiering
- Amazon Glacier
- Amazon Glacier Deep Archive
- AmazonS3 Reduced Redundancy Storage (deprecated)

### S3 Standard - General Purpose

- High durability (13 nines SLA) of objects across multiple AZ
- SLA: if we store 10 million objects in S3, we can expect to loose on average a single file per 10K years
- 99.99% availability per year
- It can sustain 2 concurrent facility failures

### S3 Standard - Infrequent Access

- Suitable for data that is less frequently accessed, but it should be retrieved quickly when it is needed
- Same durability as General Purpose, 99.9% availability
- Lower cost than General Purpose

### S3 One Zone - Infrequent Access

- Same as Standard IA, but data is stored in a single AZ
- Same durability as Standard IA. Data can be lost if an AZ goes down
- 99.5% availability per year
- Lower cost than IA

### S3 One Zone - Intelligent Tiering

- Automatically moves objects between two access tiers based on access patterns
- Has a small monthly monitoring fee
- Same durability as General Purpose, having availability of 99.9%

### S3 Glacier

- Low cost object storage for archiving/backup data
- Data is retained for longer terms (10s of years)
- Alternative to on-premise magnetic tape
- Same durability as General Purpose
- Cost per storage per month is really low, but we pay for data retrieval as well
- Each item in Glacier is called an **archive**, archives are stored in **Vaults**
- Provides 3 retrieval options:
    - Expedited (1 to 5 minutes): costs $10
    - Standard (3 to 5 hours)
    - Bulk (5 to 12 hours)
- Minimum storage duration for Glacier is 90 days

### S3 Glacier Deep Archive

- For very long term storage - cheaper than S3 Glacier
- Retrieval options:
    - Standard (12 hours)
    - Bulk (48 hours)
- Minimum storage duration is 180 days

## S3 - Moving between storage classes

- We can transition objects between storage classes in order to save money
- General rules:
    - Infrequently access documents should be moved to STANDARD_ID
    - Objects that don't need real-time access should be moved to GLACIER or DEEP_ARCHIVE
- Moving objects can be done manually or can be done via a **lifecycle configuration**
- Transaction actions: they define when should objects be transitioned from one storage to another
- Expiration actions: configuration to delete objects after a given time.\ 
    - Can be used to delete old versions of files if versioning is enabled on the bucket
    - Can be used to clean-up incomplete multi-part uploads
- Rules can be applied for a certain prefix
- Rules can be created for certain object tags

## S3 - Performance

- Amazon S3 automatically scales to high request rates, having latency of 100-200ms to get the first byte out of S3
- We can achieve:
    - 3500 PUT/COPY/POST/DELETE requests per second per prefix in a bucket
    - 5500 GET/HEAD requests per second per prefix in a bucket
- Prefix explained:
    - Example of a file in a bucket: `my-bucket/folder/subfolder/file`
    - Prefix in this case is: `/folder/subfolder/`
    - Request performance will apply to each prefix separately

### S3 KMS Limitation

- S3 Performance can be affected by KMS limits
- If encryption is enabled using SSE-KMS, we get additional requests to KMS which will apply to our KMS quota
- KMS could throttle performance, as of today we can not request a quota increase for it

### S3 Performance Optimizations

- Multi-Part upload: will split data into smaller chunks and it will upload them in parallel. It is recommended to be used for files bigger than 100MB, it is mandatory for files bigger than 5GB
- S3 Transfer Acceleration: it can increase the transfer speed in case of uploads by using an AWS edge location. Compatible with multi-part upload.
- S3 Byte-Range Fetches: can be used to speed up downloads by parallelizing GET requests. Can be used to retrieve only a part of the file

## S3 Select and Glacier Select

- Can be used to retrieve less data using SQL queries to do server side filtering
- We can filter by rows and columns. SQL statements should be simple, we can not have joins
- The purpose of S3 Select is to use less network traffic

## AWS Athena

- Serverless service to perform analytics directly against S3 files
- We can use SQL to query data from the files from S3
- It provides a JDBC/ODBC driver
- Pricing: we are charged per query amount of data scanned, we are billed for what are we using
- Supported file formats: csv, json, orc, Avro, Parquet. In the back-end it uses Presto query engine
- Athena uses cases: business intelligence, analytics, reporting, log analysis, etc.

## S3 Object Lock and Glacier Vault Lock

- S3 Object Lock: implements WORM (Write Once Read Many Model) model, meaning that it guarantees that a file is only written once and it can not be deleted until the lock is removed
- Glacier Vault Lock: same WORM model is implemented, locket file can not be changed as long as the lock is active
- Helpful for compliance and data retention