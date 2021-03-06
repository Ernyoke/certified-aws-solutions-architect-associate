# AWS Config

- Helps with auditing and recording compliance of AWS resources
- Helps record configurations and changes over time
- Provides the ability of storing the configuration data into S3 where it can be analyzed by Athena
- Problems AWS Config Solves:
    - Check if there is unrestricted SSH access to a security group
    - Check if buckets have public access
    - Find out how did an ALB configuration change over time
- We can receive alerts (SNS notifications) for any change
- AWS Config is a per region service, but it can be aggregated across regions and accounts

## AWS Config Resource

- Ability to view the compliance of a resource over time
- Ability to view configuration of a resource over time
- View CloudTrails API calls if enabled

## AWS Config Rules

- AWS provides a set of managed config rules (over 75) which can be used by the users
- Users can also make custom config rules (a rule must be defined using AWS Lambda)
- Example of custom rules user can make:
    - Evaluate if each EBS disk is of type GP2
    - Evaluate if each EC2 instance is of type t2.micro
- Rules can be evaluated/triggered:
    - For each config change
    - At regular time intervals
- Evaluation of rules can trigger CloudWatch events if the rule is non-compliant
- Rules can have auto remediations: if a resource is not compliant, the is an option to trigger auto remediation, example: stop instances with non-approved tags
- **AWS Config Rules do not prevent actions from happening (no deny)**

## CloudWatch vs CloudTrail vs Config

- CloudWatch
    - Performance monitoring (metrics, CPU, network, etc.) and dashboards
    - Events and alerting
    - Log aggregation and analysis
- CloudTrail
    - Record API calls made within an account
    - Define trails for specific resources
    - It is a global service
- Config
    - Record configuration changes
    - Evaluate resources against compliance rules
    - Get timeline of changes and compliance