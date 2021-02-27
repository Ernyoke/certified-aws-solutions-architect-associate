# AWS CloudWatch

## AWS CloudWatch Metrics

- CloudWatch provides metrics for every service in AWS
- A **Metric** is a variable to monitor: CPUUtilization, NetworkIn, etc.
- Metrics in CloudWatch belong to **namespaces**
- A **Dimension** is an attribute of a metric, examples: instance id, environment name, etc.
- We can have up to 10 dimensions per metrics
- Metrics have **timestamps**
- We can create CloudWatch dashboards from metrics

## EC2 Details Monitoring

- EC2 instance metrics are gathered every 5 minutes
- We can enable details metrics (for a cost) which will allow gathering every 1 minute
- We can use detailed monitoring if we want more prompt scale for ASG
- Free tier allows to have 10 details monitoring metrics
- EC2 memory usage by default is not pushed to CloudWatch, we should have a custom metric for it

## CloudWatch Custom Metrics

- We have the possibility to send our own custom metrics to CloudWatch
- We can use dimensions (attributes) to segment our metrics
- Metrics resolution by default is 1 minute, but we can have higher resolutions up to 1 second for a higher cost
- We can send metrics by using the **PutMetricsData** API call
- In case of errors we should use exponential back-off

## CloudWatch Dashboards

- Great way to setup dashboards for quick access to key metrics
- Dashboards are global
- Dashboards can include graphs from different regions
- We can change the time zone and time rage for each dashboard
- We can set up automatic refresh (10s, 1m, 2m, 5m, 15m)
- Pricing:
    - 3 dashboards (up to 50 metrics) for free
    - $3/dashboard/month

## CloudWatch Logs

- Applications can send logs to CloudWatch using the SDK
- Also, CloudWatch can collects logs from:
    - Elastic Beanstalk: collection of logs from applications
    - ECS: collections of logs from containers
    - AWS Lambda: collection from functions
    - VPL Flow Logs
    - API Gateway
    - CloudTrail based on filter
    - CloudWatch log agents: from EC2 machines
    - Route53: logs for DNS queries
- CloudWatch logs can be saved to:
    - Batch exporting to S3 for archival
    - Stream logs to ElasticSearch cluster for further analytics
- Log storage architecture:
    - Log groups: arbitrary name, usually representing the name of an application
    - Log stream: instances within application/log files/containers
- We can define a log expiration policy: never expire, 30 days, etc.
- Using the AWS CLI we can tail logs
- To send logs to CloudWatch, we have to make sure the IAM permissions are correct
- Logs can be encrypted at group level using KMS

### Log Metric Filter and Insights

- CloudWatch Logs can use filter expressions
    - For example, find a specific IP inside of a log
    - Metric filters can be used to trigger alarms
- CloudWatch Logs Insights: can be used to query logs and add queries to CloudWatch Dashboards

## CloudWatch Agent

- By default no logs from EC2 machines will go to CloudWatch
- We need to run a CloudWatch agent on EC2 to push the log files to CloudWatch
- We have to make sure the IAM permissions are correct for the EC2 instance
- CloudWatch log agents can be installed to on-premise instances

### CloudWatch Logs Agent and Unified Agent

- CloudWatch Logs Agent:
    - Old version of the agent
    - Can only send data to CloudWatch Logs
- CloudWatch Unified Agent:
    - Can collect additional system level metrics
    - Can collect logs and send them to CloudWatch logs
    - Can collect metrics
    - It can have centralized configuration using SSM Parameter Store

### CloudWatch Unified Agent Metrics

- Metrics are collected from Linux Servers running on EC2 instances
- Can collect information from:
    - CPU (active, guest, idle, system, user, steal)
    - Disk metrics (free space, used, total)
    - Disk IO (reads, writes, bytes, iops)
    - RAM (free, inactive, used, total, cached)
    - Netstat (number of TCP and UDP connections, net packages)
    - Processes (total, dead, blocked, idle, running, sleep)
    - Swap Space
- Out of the box metrics for EC2 - disk, CPU, network, for more granularity use CloudWatch Unified Agent

## CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Alarms can go to Auto Scaling, EC2 Actions, SNS notifications
- There are various options for alarm metrics: sampling, percentage, max, min, etc.
- Alarm states:
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period:
    - Length of time in seconds to evaluate the metric
    - In case we are using high resolution custom metrics, we can chose between 10 or 30 seconds for firing the alarm

## EC2 Instance Recovery

- Status Checks:
    - Instance status = check the EC2 VM
    - System check = check the underlying hardware
- If one of these alarms are triggered, we can have an action called Instance Recovery. This will trigger some internal mechanism in AWS to recover the instance
- After an instance recovery we will have the same private, public, elastic IP, same metadata and placement group
- Any data stored on an instance store will not be kept

## AWS CloudWatch Events

- CloudWatch events can be:
    - Scheduled: cron job
    - Event pattern: event rules to react to a service doing something
- CloudWatch events can trigger a Lambda function, or can send SQS/SNS/Kinesis messages
- A CloudWatch event creates a small JSON document to give information about the change
