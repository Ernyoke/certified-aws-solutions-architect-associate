# Disaster Recovery

- Disaster: any event that has a negative impact on a company's business continuity or finances
- Disaster Recovery (DR) is about preparing for and recovering from a disaster
- Disaster recovery solutions:
    - On-premise to on-premise: traditional DR, expensive
    - On-premise to cloud: hybrid recovery
    - AWS Cloud Region A to AWS Cloud Region B

## RPO and RTO

- RPO - Recovery Point Objective: How often we create backups. Time between the RPO and the disaster is the data loss
- RTO - Recovery Time Objective: The point in time when the recovery finishes. The time between the disaster and the RTO is downtime

## Disaster Recovery Strategies

- Backup and Restore: high RPO, cheap, easy to manage and accomplish
- Pilot Light:
    - A small version of the app is always running in the cloud
    - Useful for critical core (pilot light)
    - Similar to backup and restore strategy
    - Faster than backup and restore as critical system are already running
- Warm Standby
    - Full system is up and running but at a minimal size
    - Upon disaster we can scale to production load
- Hot Site / Multi Site Approach
    - Very low RTO - very expensive
    - Full production scale is running on the cloud
- All AWS Multi Region

## Disaster Recovery Tips

- Backups:
    - EBS Snapshots, RDS, automated backups, snapshots, etc.
    - Regular pushes to S3/S3 IA/Glacier, Lifecycle Policy, Cross region replication
    - From on-premise: Snowball or Storage Gateway
- High Availability:
    - Use Route53 to migrate DNS over from region to region
    - RDS Multi-AZ, ElastiCache Multi-AZ, EFS, S3
    - Site to site VPN as recovery from Direct Connect
- Replication:
    - RDS Replication (Cross Region), AWS Aurora + Global Databases
    - Database replication from on-premise to RDS
    - Storage Gateway
- Automation:
    - CloudFormation/Elastic Beanstalk to recreate a whole new environment
    - Recover/Reboot EC2 instances with CloudWatch if alarm is in fail state (ALARM)
    - AWS Lambda for customized automation
- Chaos
    - Netflix has a "simian-army" randomly terminating EC2 instances

## On-Premise Strategy with AWS

- Ability to download Amazon Linux 2 AMI as a VIM (iso format)
- VM Import/Export:
    - Ability to migrate existing applications into EC2
    - Ability to create a DR repository for on-premise VMs
    - Ability to export back the VMs form EC2 to on-premise
- AWS Application Discovery Service:
    - Gather information about on-premise servers to plan a migration
    - Provides information about server utilization and dependency mappings
    - Track all migrations with AWS Migration Hub
- AWS Database Migration Service (DMS)
- AWS Server Migration Service (SMS):
    - Incremental replication of on-premise live servers to AWS
