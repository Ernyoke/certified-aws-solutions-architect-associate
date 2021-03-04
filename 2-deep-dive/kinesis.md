# AWS Kinesis

- Kinesis is a managed alternative to Apache Kafka
- It is a big data stream tool, which allows to stream application logs, metrics, IoT data, click streams, etc.
- Compatible with many streaming frameworks (Spark, NiFi, etc.)
- Data is automatically replicated to 3 AZ
- Kineses offers 3 types of products:
    - **Kinesis Streams**: low latency streaming ingest at scale
    - **Kinesis Analytics**: perform real-time analytics on streams using SQL
    - **Kinesis Firehose**: load streams into S3, Redshift, ElasticSearch

## Kinesis Streams Overview

- Streams are divided in ordered Shards/Partitions
- For higher throughput we can increase the size of the shards
- Data retention is 1 day by default, can go up to 7 days
- Kinesis Streams provides the ability to reprocess/replay the data
- Multiple applications can consume the same stream, this enables real-time processing with scale of throughput
- Kinesis is not a database, once the data is inserted, it can not be deleted

### Kinesis Stream Shards

- One stream is made of many different shards
- 1MB/s or 1000 messages at write PER SHARD
- 2MB/s read PER SHARD
- Billing is done per shard provisioned, we can have a many shards as we want as long as we accept the cost
- Ability to batch the messages per calls
- The number of shards can evolve over time (reshard/merge)
- **Records are ordered per shard!**

### Kinesis Streams API - Put Records

- Data must be sent form the PutRecords API to a partition key
- Data with the same key goes to the same partition (helps with ordering for a specific key)
- Messages sent get a sequence number
- Partition key must be highly distributed in order to avoid hot partitions
- In order to reduce costs, we can use batching with PutRecords API
- It the limits are reached, we get a *ProvisionedThroughputException*

### Exceptions

- ProvisionedThroughputException Exceptions:
    - Happens when the data value exceeds the limit exposed by the shard
    - In order to avoid the, we have to make sure we don't have hot partitions
    - Solutions:
        - Retry with back-off
        - Increase shards (scaling)
        - Ensure the partition key is good

### Consumers

- Consumers can use CLI or SDK, or the Kinesis Client Library (in Java, Node, Python, Ruby, .Net)
- Kinesis Client Library (KCL) uses DynamoDB to checkpoint offsets
- KCL uses DynamoDB to track other workers and share work amongst shards

### Security

- Control access / authorization using IAM policies
- Encryption in flight using HTTPS endpoints
- Encryption at rest using KMS
- Possibility to encrypt/decrypt data client side
- VPC Endpoints available for Kinesis to be access within VPCs