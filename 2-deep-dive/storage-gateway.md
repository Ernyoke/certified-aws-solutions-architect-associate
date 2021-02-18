# Storage Gateway

## Hybrid Cloud for Storage

- Hybrid cloud:
    - Part of a company's infrastructure is in the public cloud (like AWS)
    - Part of a company's infrastructure is on-premise
- S3 is a proprietary storage technology (unlike EFS/NFS), it can be exposed to on-premise servers through a storage gateway

## Storage Gateway Introduction

- Bridge between on-premise data and cloud data in S3
- Uses cases for storage gateway with S3: disaster recovery, backup and restore, tiered storage
- AWS provides 3 types of storage gateways:
    - **File Gateway**: allows us to view files from the local files system, but this files are backed by S3
    - **Volume Gateway**: same as file gateway but with volumes
    - **Tape Gateway**: used for backup and recovery

### File Gateway

- Configured S3 buckets are accessible using NFS and SMB protocols
- Supports S3 Standard, S3 IA, One Zone IA
- Each buckets will have its own IAM roles in order to be accessed by the file gateway
- Most recently used data is cached in the file gateway
- File Gateway can be mounted on many servers (because of the NFS protocol)

### Volume Gateway

- Block storage using iSCSI protocol backed by S3
- EBS snapshots are created time to time which are stored in S3, these will help use to restore un-premise volumes
- Cached volumes: low latency access to the most recently used data
- Stored volumes: entire dataset is on premise, scheduled buckets are stored in S3
- Volumes are usually mounter using iSCSI protocol, for on-premise it will look like a local volume

### Tape Gateway

- Some companies have backup processes using physical tapes
- With tape gateway these companies can use the same process, but the data will backed into the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Backup processes using iSCSI interface will work as well with tape gateway

### File Gateway - Hardware Appliance

- Using file gateway means we need virtualization, otherwise we can use a **File Gateway Hardware Appliance**
- It is an actual hardware which can be bought from amazon.com
- Helpful for daily NFS backup in small data centers

## Storage Gateway Summary

- File access / NFS => File Gateway (backed by S3)
- Volumes / Block Storage / iSCSI => Volume Gateway (backed by S3 with EBS snapshots)
- VTL Tape solution / Backup with iSCSI => Tape Gateway (backed by S3 and Glacier)