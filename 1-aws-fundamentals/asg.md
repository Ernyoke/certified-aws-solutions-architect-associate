# Auto Scaling Groups

- Applications may encounter different amount of load depending on their usage span
- In cloud we can create and get rid of resources (servers) quickly
- The goal of an Auto Scaling Group (ASG) is to:
    - Scale out (add more EC2 instances) to match the increased load
    - Scale in (remote EC2 instances) to match a decreased load
    - Ensure we have a minimum and a maximum number of machines running
    - Automatically register new instances to a load balancer

## ASG Attributes

- Launch configuration - consists of:
    - AMI + Instance Type
    - EC2 User Data
    - EBS Volumes
    - Security Groups
    - SSH Key Pair
- Min size, max size, initial capacity
- Network + subnets information
- Load balancer information
- Scaling policies - what will trigger a scale out/scale in

## Auto Scaling Alarms

- It is possible to scale an ASG based on CloudWatch alarms
- An alarm monitors a metric (such as Average CPU)
- **Metrics are computed for the overall ASG instances**
- Based on alarms we can create:
    - Scale-out policies
    - Scale-in policies

## Auto Scaling New Rules

- It is possible to define "better" auto scaling rules managed directly by EC2 instances, for example:
    - Target Average CPU Usage
    - Number of requests on teh ELB per instance
    - Average Network In/Out
- These rules are easier to set up and to reason about then the previous ones

## Auto Scaling Based on Custom Metrics

- We can scale based on a custom metric (ex: number of connected users to the application)
- In order to do this we have to:
    1. Send a custom metric request to CloudWatch (PutMetric API)
    2. Create a CloudWatch alarm to react to values of the metric
    3. Use the CloudWatch alarm as a scaling policy for ASG

## ASG Summary

- Scaling policies can be on CPU, Network, etc. and can even be based on custom metrics or based on a schedule
- ASGs can use launch configurations or launch templates (newer version)
    - Launch configurations allow to specify one instance type
    - Launch templates allow to use a spot fleet of instances
- To update an ASG, we must provide a new launch configuration/template. The underlying EC2 instances will be replaced over time
- IAM roles attached to an ASG will be assigned to the launched EC2 instances
- ASGs are free. We pay for the underlying resources being launched (EC2 instances, attached EBS volumes, etc.)
- Having instances under an ASG means that if they get terminated for any reason, the ASG will automatically create new ones as a replacement
- ASG can terminate instances marked as unhealthy by a load balancer and obviously replace them
- Health checks - we can have 2 types of health checks:
    - EC2 health checks - instances is recreated if the EC2 instance fails to respond to health checks
    - ELB health checks - instance is recreated if the ELB health checks fail, meaning that the application is down for whatever reason

## ASG Scaling Policies

- **Target Tracking Scaling**
    - Most simple and easy to setup
    - Example: we want the average ASG CPU to stay around 40%
- **Simple/Step Scaling**
    - Example: 
        - When a CloudWatch alarm is triggered (example average CPU > 70%), then add 2 units
        - When a CloudWatch alarm is triggered (example average CPU < 30%), then remove 1 unit
- **Scheduled Actions**
    - Can be used if we can anticipate scaling based on known usage patterns
    - Example: increase the min capacity to 10 at 5 PM on Fridays

### Scaling Cool-downs

- The cool-down period helps to ensure that our ASG doesn't launch or terminate additional instances before the previous scaling activity takes effect
- In addition to default cool-down for ASG we can create cool-downs that apply to specific *simple scaling policy*
- A scaling-specific cool-down overrides the default cool-down period
- Common use case for scaling-specific cool-downs is when a scale-in policy terminates instances based in a criteria or metric. Because this policy terminates instances, an ASG needs less time to determine wether to terminate additional instances
- If the default cool-down period of 300 seconds is too long, we can reduce costs by applying a scaling-specific cool-down of 180 seconds for example
- If our application is scaling up and down multiple times each hour, we can modify the ASG cool-down timers and the CloudWatch alarm period that triggers the scale

## Suspend and Resume Scaling Processes

- We can suspend and then resume one or more of the scaling processes for our ASG. This can be useful when we want to investigate a configuration problem or other issue with our web application and then make changes to our application, without invoking the scaling processes.
- We can manually move an instance from an ASG and put it in the standby state
- Instances in standby state are still managed by Auto Scaling, are charged as normal, and do not count towards available EC2 instance for workload/application use. Auto scaling does not perform health checks on instances in the standby state. Standby state can be used for performing updates/changes/troubleshooting etc. without health checks being performed or replacement instances being launched.

## ASG for Solutions Architects

- **ASG Default Termination Policy**
    1. Find the AZ which has to most number of instances
    2. If there are multiple instances to choose from, delete the one with the oldest launch configuration
- Lifecycle Hooks
    - By default as soon as an instance is launched in an ASG, the instance goes in service
    - ASGs provide the ability to perform extra steps before the instance goes in service
    - Also, we have he ability to perform some actions before the instance is terminated
    - Lifecycle hooks diagram: https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html
- Launch Templates vs. Launch Configurations
    - Both allow to specify the AMI, the instance type, a key par, security groups and the other parameters that we use to launch EC2 instances (tags, user-data, etc.)
    - Launch Configurations are considered to be legacy:
        - They must be recreated every time
    - Launch Templates:
        - They can have multiple versions
        - They allow parameter subsets used for partial configuration for re-use and inheritance
        - We can provision both On-Demand and Spot instances (or a mix of two)
        - We can use the T2 unlimited burst feature
        - Recommended by AWS
