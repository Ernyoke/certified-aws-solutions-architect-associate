# AWS DataSync

- Can be used to move large amount of data from on-premise to AWS
- Ability to synchronize to S3, EFS, FSx for Windows
- Ability to move data from NAS of file system to NFS or SMB
- Replication tasks can be scheduled hourly, daily or weekly
- We can leverage the DataSync agent to connect to our systems

## Transferring large amount of data to AWS

- Problem: transfer 200TB of data in the cloud with a 100 Mbps internet connection.
- Solutions:
    - Over the internet / Site-to-Site VPN:
        - Immediate to setup
        - It will take approximately 185 days
    - Over direct connect (1 Gbps)
        - Will take long time for the initial setup (over 1 month)
        - The data will be transferred on aprox. 18.5 days
    - Snowball:
        - Will take 2 to 3 snowballs in parallel
        - Takes about 1 week for end-to-end transfer
        - Can be combined with DMS
- For on-going replication/transfers: Site-to-Site VPN or DX with DMS or DataSync