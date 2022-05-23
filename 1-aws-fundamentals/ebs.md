# EBS - Elastic Block Storage

- An EC2 instances loses its root volume when it is manually terminated
- Unexpected terminations may happen
- Sometimes we need a way to store instance data somewhere
- An EBS (Elastic Block Store) Volume is a network drive which can be attached to an EC2 instance
- It allows for the instances to persist date
- EBS is a network drive:
    - It uses the network to communicate with the instance, which can introduce latency
    - It can be detached from an EC2 and attached ot another
- EBS volumes are locked to an AZ
- To move a volume across, we need to create a snapshot
- EBS volumes have a provisioned capacity (size in GB and IOPS)
- Billing is done for all provisioned capacity even if the capacity is not fully used

## EBS Volume Types

- EBS Volumes can have 4 types:
    - GP2 (SSD): general purpose SSD volume that balances price and performance
    - IO1 (SSD): highest performance SSD volume for mission-critical low-latency or high-throughput workloads
    - ST1 (HDD): low cost HDD volume designed for frequent access, throughput-intensive workloads
    - SC1 (HDD): for less frequently accessed data
- EBS Volumes are characterized in Size | Throughput | IOPS (I/O Operations per second)
- Only GP2 and IO1 can be used as boot volumes

## EBS Volume Types - Deep Dive

### GP2

- Recommended for most workloads
- Can be system boot volume
- Can be used for virtual desktops, low-latency applications, development and test environments
- Size can range from 1GiB to 16TiB
- Small GP2 volumes can burst IOPS to 3000
- Max IOPS is 16000
- We get 3 IOPS per GiB, which means at 5334GiB we are the max IOPS size

### IO1 

- Recommended for business critical applications which require sustained IOPS performance, or more than 16000 IOPS per volume
- Recommended for large database workloads
- Size can be between 4Gib and 16 TiB
- The maximum ratio of provisioned IOPS per requested volume size is 50:1
- Max IOPS for IO1/2 volumes is 64000 IOPS for instances built on Nitro System and 32000 for other type of instances

### ST1

- Recommended for streaming workloads
- It has fast throughput at low price
- Can not be a root volume
- Size can be between 500Gib and 16TiB
- Max IOPS is 500
- Max throughput 500 MiB/Sec

### SC1

- Throughput oriented storage for large volumes of data which is infrequently accessed
- Can not be a boot volume
- Max IOPS is 250, max throughput 250MiB/sec

### Limits

- SSD, General Purpose – gp2
    – Volume size 1 GiB – 16 TiB
    – Max IOPS/volume 16,000
- SSD, Provisioned IOPS – i01
    – Volume size 4 GiB – 16 TiB
    – Max IOPS/volume 64,000
– HDD, Throughput Optimized – (st1)
    – Volume size 500 GiB – 16 TiB 
    - Throughput measured in MB/s, and includes the ability to burst up to 250 MB/s per TB, with a baseline throughput of 40 MB/s per TB and a maximum throughput of 500 MB/s per volume
- HDD, Cold – (sc1)
    – Volume size 500 GiB – 16 TiB.
    - Lowest cost storage – cannot be a boot volume
– These volumes can burst up to 80 MB/s per TB, with a baseline throughput of 12 MB/s per TB and a maximum throughput of 250 MB/s per volume: HDD, Magnetic – Standard – cheap, infrequently accessed storage – lowest cost storage that can be a boot volume

## EBS Snapshots

- Snapshots are incremental - only the changed blocks are backed up
- EBS backups use IO and we should not run them while the application is handling a lot of traffic
- Snapshots are stored in S3 (we are not able to see them)
- It is not necessary to detach the volume to do a snapshot, but it is recommended
- An account can have up to 100k snapshots
- We can make an image (AMI) out of a snapshot, snapshots can be copied across AZs
- EBS volumes restored from snapshots need to be pre-warmed (using `fio` or `dd` commands to read the entire volume)
- Snapshots can be automated using **Amazon Data Lifecycle Manager**

## EBS Migrations

- EBS Volumes are locked to a specific AZ
- To migrate it to a different AZ (or region) we have to do the following:
    - Create a snapshot from the volume
    - (optional) Copy the volume to a different region
    - Create a volume from the snapshot in the AZ of choice

## EBS Encryption

- When we create an encrypted EBS volume, we get the following:
    - Data at rest is encrypted inside the volume
    - All the data in flight moving between the instance and the volume is encrypted
    - All snapshots are encrypted
    - All volumes created from the snapshots will be encrypted
- Encryption and decryption are handled transparently by EBS system
- Encryption may have a minimal impact on latency
- EBS Encryption leverages keys from KMS (encryption algorithm is AES-256)
- Copying an unencrypted snapshot allows encryption
- Encrypt an unencrypted EBS volume:
    1. Create an EBS snapshot from the volume
    2. Copy the snapshot an enable encryption on the process
    3. Create a new EBS volume from the snapshot (the volume will be encrypted)
    4. Attach the encrypted volume to an instance

## EBS vs Instance Store

- Some instances do not come with a root EBS volume
- Instead, they come with an **instance store** (ephemeral storage)
- An instance store is  a physically attached to the machine (EBS is a network drive)
- Pros of instance stores:
    - Better I/O performance
    - Good for buffer, cache, scratch data, temporary content
    - Data survives a reboot
- Cons of instance stores:
    - On stop or termination of the instance, the data from the instance store is lost
    - An instance store can not be resized
    - Backups of an instance store must be done manually by the user
- An instance store is:
    - A physical disk form the physical server where the EC2 instance runs
    - Very Hight IOPS disk
    - A disk up to 7.5 TiB, stripped to reach 30 TiB
    - A block storage (just like EBS)
    - Can not be increased in size
    - An ephemeral storage (risk of data loss if hardware fails)

## EBS RAID Options

- EBS is already redundant storage (replicated within an AZ)
- If we want to increase IOPS of if we want to mirror an EBS volume we can mount EBS volumes in parallel RAID settings
- RAID is possible as long as the OS supports it
- Some RAID options are:
    - RAID 0
    - RAID 1
    - RAID 5, RAID 6 are not recommended for EBS
- **RAID 0**: used for increased performance. We can combine to or more volumes and what we get is the total number of disk space and I/O
    - If one of the disks fail, all the logical data is lost
    - Use cases:
        - Applications with lot of IOPS but without the need for fault-tolerance
        - A database with builtin replication
- **RAID 1**: used for increased fault-tolerance. Mirroring a volume to another.
    - If one the disks fails, the logical volume will still work
    - We have to send the data to two EBS volumes at the same time
    - Use cases:
        - Applications that need increased fault-tolerance
        - Applications which need to service disks
