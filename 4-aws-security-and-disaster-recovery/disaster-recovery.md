# Disaster Recovery

- Disaster: any event that has a negative impact on a company's business continuity or finances
- Disaster Recovery (DR) is about preparing for and recovering from a disaster
- Disaster recovery solutions:
    - On-premise to on-premise: traditional DR, expensive
    - On-premise to cloud: hybrid recovery
    - AWS Cloud Region A to AWS Cloud Region B

## RPO and RTO

- RPO - Recovery Point Objective: How often we create backups. Time between the RPO and the disaster is the data loss
- RPT - Recovery Time Objective: The point in time when the recovery finishes. The time between the disaster and the RTO is downtime

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

