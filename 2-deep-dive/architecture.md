# Classic Architecture Discussion

- This section discusses basic architectural solutions combining technical offerings provided by AWS

## Stateless Web Apps: WhatIsTheTime.com

- WhatIsTheTime.com: allows people to know what time it is
- No database required
- We want to start small and can accept downtime at the beginning
- We want to be able to fully scale vertically and horizontally with no downtime

- Considerations when building the architecture:
    - Public IP vs private IP in case of EC2 instances
    - Elastic IP vs Route53 vs Load Balancers
    - Route53 TTL A records vs Alias Records
    - Maintaining EC2 instance scaling manually vs using an ASG
    - Multi AZ deployments to have disaster recovery
    - ELB Heath Checks
    - Security Groups Rules
    - Reservation of capacity for cost saving when possible
    - We are considering the 5 pillars of a well architected application: cost, performance, reliability, security and operational excellence

## Stateful Web Apps: MyClothes.com

- MyClothes.com: allows people to buy clothes online
- There is a shopping cart for every user
- The application can have hundreds of users using the app in the same time
- We need to scale, maintain horizontal scalability and keep the app as stateless as possible
- Users should not loose their shopping cart while navigating the site
- Users should have their details in a database

- Consideration when building the architecture:
    - ELB sticky sessions
    - Web clients for storing cookies and making the application stateless
    - Session id + session cache which can be ElastiCache or DynamoDB
    - ElastiCache can be used to cache data from RDS
    - Multi AZ for disaster recovery
    - RDS:
        - We can use it for store user data
        - We can have read replicas for scaling reads
        - We can enable Multi AZ for disaster recovery 
- Tight security using security groups

## Instantiating Applications Quickly

- When launching a full stack application, it can take time to:
    - Install the application
    - Insert initial (or recovery) data
    - Configure everything
    - Launch the application
- In order to solve this we can use:
    - For EC2 Instances:
        - **Golden AMI**: install the application, OS, dependencies etc. beforehand and launch the EC2 instance from the Golden AMI
        - **Bootstrap using User Data**: for dynamic configuration we can use User Data scripts
    - For RDS databases we can restore the database from a snapshot
    - For EBS Volumes we can also restore the data from a snapshot