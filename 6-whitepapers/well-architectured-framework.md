# Well Architectured Framework

## General Guiding Principles

- Stop guessing capacity needs
- Test systems at production scale
- Automate to make architectural experimentation easier
- Allow for evolutionary architectures, design based on changing requirements
- Drive architecture using data
- Improve through game days, simulate applications for flash sale days

## 5 pillars

1. Operational Excellence
2. Security
3. Reliability
4. Performance Efficiency
5. Cost Optimization
- **They are not something to balance, or trade-offs, they are synergy!**

## AWS Well-Architected Tool

- It is an AWS console service
- We can define workload to track our architecture's performance over time
- We can start a review based on the 5 pillars by answering a bunch of questions
- At the end we can generate a report

## 1st pillar: Operational Excellence

- Includes the ability to run and monitor systems to deliver business value and to continually improve supporting processes and procedures
- Design principles:
    - Perform operations as code - infrastructure as code
    - Annotate documentation - automate creation of annotated documentation after every build
    - Make frequent, small, reversible changes
    - Refine operations procedure frequently and ensure team members are familiar with it
    - Anticipate failure and learn from operational failure
- In terms of AWS services:
    - Prepare:
        - CloudFormation
        - AWS Config
    - Operate:
        - CloudFormation
        - Config
        - CloudTrail
        - CloudWatch
        - XRay
    - Evolve:
        - CloudFormation
        - CodeBuild
        - CodeDeploy
        - CodePipeline

## 2nd pillar: Security

- Includes the ability to protect information, systems and assets while delivering business value through risk assessments and mitigation strategies
- Design principles:
    - Implement a string identity foundation
    - Enable traceability
    - Apply security at all layers
    - Automate security best practices
    - Protect data in transit and at rest
    - Keep people away from data
    - Prepare for security events
- In terms of AWS services:
    - Identity and access management:
        - IAM
        - AWS-STS
        - MFA token
        - AWS Organizations
    - Detective controls:
        - AWS Config
        - CloudTrail
        - CloudWatch
    - Infrastructure protection:
        - CloudFront
        - VPC
        - AWS Shield
        - AWS WAF
        - Amazon Inspector
    - Data protection:
        - KMS
    - Incident response:
        - IAM
        - AWS CloudFormation
        - CloudWatch Events

## 3rd pillar: Reliability

- Ability of a system to recover from infrastructure or service disruptions, dynamically acquire computing resources to meet demand and mitigate disruptions such as misconfigurations or transient network issues
- Design principles:
    - Test recovery procedures
    - Automate recover from failure
    - Scale horizontally to increase aggregate system availability
    - Stop guessing capacity, use auto-scaling
    - Manage change in automation
- In terms of AWS services:
    - Foundations:
        - IAM
        - VPC
        - Service Limits
        - AWS Trusted Advisor
    - Change management:
        - Auto Scaling
        - CloudWatch
        - CloudTrail
        - Config
    - Failure Management:
        - Backups
        - CloudFormation
        - S3, Glacier
        - Route53

## 4th pillar: Performance Efficiency

- Includes the ability to use computing resources efficiently to meet system requirements and to maintain that efficiency as demand changes and technologies evolve
- Design principles:
    - Democratize advanced technologies
    - Go global in minutes
    - Use serverless architectures
    - Experiment more often
    - Mechanical sympathy, be aware of all AWS services
- In terms of AWS services:
    - Selection:
        - AWS Auto Scaling
        - AWS Lambda
        - EBS
        - S3
        - RDS
    - Review:
        - CloudFormation
        - AWS News Blog
    - Monitoring:
        - CloudWatch
        - AWS Lambda
    - Tradeoffs:
        - ElastiCache
        - Snowball
        - CloudFront
    
## 5th pillar: Cost Optimization

- Includes the ability to run systems to deliver business value at the lowest price point
- Design principles:
    - Adopt a consumption mode, pay only for what you use
    - Measure overall efficiency
    - Stop spending money on data center operations
    - Analyze and attribute expenditure
    - Use managed and application level services to reduce cost of ownership
- In terms of AWS services:
    - Expenditure awareness:
        - AWS Budgets
        - AWS Cost and Usage Report
        - AWS Cost Explorer
        - Reserved Instance Reporting
    - Cost-effective resources:
        - Spot instances
        - Reserved Instances
        - S3 Glacier
    - Matching supply and demand:
        - Auto Scaling
        - AWS Lambda
    - Optimize over time:
        - AWS Trusted Advisor
        - AWS Cost and Usage Report
        - AWS News Blog