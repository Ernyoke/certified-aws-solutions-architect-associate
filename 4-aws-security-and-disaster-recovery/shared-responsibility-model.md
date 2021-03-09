# AWS Shared Responsibility Model

- AWS responsibility - Security **of** the Cloud
    - Protecting infrastructure (hardware, software, facilities and networking) that runs all of the AWS services
    - Managed services like S3, DynamoDB, RDS etc
- Customer responsibility - Security **in** the Cloud
    - For EC2 instance, customer is responsible for management of the guest OS (including security patches), firewall and network configuration, IAM etc.

## Example for RDS:

- AWS responsibility:
    - Manage the underlying EC2 instance, disable SSH access
    - Automate DB patching
    - Automate OS patching
    - Audit the underlying instance and disk. Guarantee it function
- Customer responsibility:
    - Check the ports/IP/SG inbound rules in DB's SG
    - In-database user creation and permissions
    - Creating a database with or without public access
    - Ensure parameter groups or DB is configured to only allow SSL connections
    - Database encryption settings

## Example for S3:

- AWS responsibility:
    - Guarantee we get unlimited storage
    - Guarantee we get encryption
    - Ensure separation of the data between different customers
    - Ensure AWS employees can't access customer's data
- Customer responsibility:
    - Bucket configuration
    - Bucket policy / public settings
    - IAM user and roles
    - Enable encryption
