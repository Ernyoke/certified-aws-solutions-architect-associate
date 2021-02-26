# EFS - Elastic File System

- EFS is a managed NFS (network file system) that can be mounted on many EC2 instances
- EFS works with EC2 instances across multi AZs
- EFS is highly available, scalable, but also more expensive (3x GP2) than EBS
- EFS is pay per use
- Use cases: content management, web service, data sharing, Wordpress
- Uses NFSv4.1 protocol
- We can use security groups to control access to EFS volumes
- EFS is only compatible with Linux based AMIs (not Windows)

## EFS Performance and Storage Classes

- EFS Scale
    - Thousands of concurrent NFS clients, 10 GB+ per second throughput
    - It can grow to petabyte scale NFS automatically
- Performance mode (can be set at EFS creation time)
    - General purpose (default): recommended for latency-sensitive use cases: web server, CMS, etc.
    - Max I/O - higher latency, throughput, highly parallel, recommended for big data, media processing
- Storage tiers: lifecycle manage feature
    - Standard: for frequently accessed files
    - Infrequent access (EFS-IA): there is a cost to retrieve files, lower price per storage

## EBS vs EFS

- EBS volumes
    - Can be attached to only one instance at a time
    - Are locked at the AZ level
    - GP2: IO increases if the disk size increases
    - IO1: can increase IO independently
    - To migrate an EBS across AZ:
        - Take a snapshot
        - Restore the volume from the snapshot
    - Root EBS volumes get terminated by default in the EC2 instance is terminated (this feature can be disabled)
- EFS
    - Can be mounted to multiple instances across AZs via EFS mount targets
    - Available only for Linux instances
    - EFS has a higher price point than EBS
    - EFS is pay per second, we can leverage EFS-IA for cost saving
