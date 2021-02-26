# AWS Lambda Functions

## Serverless Introduction

- Serverless is a new paradigm in which the developers do not have to manage servers anymore
- Initially Serverless == FaaS (Function as a Service), but now it can mean a lot more
- Serverless was pioneered by AWS Lambda but now the concept also includes anything that is not managed: databases, messaging, storage, etc.
- Serverless does not meant there are no servers, it means that we are not responsible for managing the underlying infrastructure

## Serverless in AWS

- AWS Lambda
- DynamoDB
- AWS Cognito
- AWS API Gateway
- Amazon S3
- AWS SNS and SQS
- AWS Kinesis Data Firehose
- Aurora Serverless
- Step Functions
- Fargate

## AWS Lambda Introduction

- AWS Lambda Functions are virtual functions, which means there are no servers to manage
- There are limited to time - they require short execution times
- They run on-demand - we are only billed when the function is running
- Scaling is automated

## Benefits of Lambda

- Easy pricing
    - Pay per request and compute time
    - Generous free tier: 1 million AWS requests for free and 400K GBs compute time
- Lambda is integrated with the AWS suite of services
- It supports a lot of programming languages
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per functions (up to 3GB or RAM)
- Increasing the RAm will also increase the CPU and network
- Supported languages:
    - NodeJS (JavaScript)
    - Python
    - Java
    - C# (.NET Core and PowerShell)
    - Golang
    - Ruby
    - Custom Runtime API (community supported, example Rust)
- **Docker does not run on AWS Lambda!**

## AWS Lambda Integrations

- Main ones:
    - API Gateway
    - Kinesis
    - DynamoDB
    - S3
    - CloudFront
    - CloudWatch Events and EventBridge
    - CloudWatch Logs
    - SNS and SQS
    - AWS Cognito

## AWS Lambda Pricing

- Up to date information can be found at: https://aws.amazon.com/lambda/pricing/
- Pay per call:
    - First 1 million requests are free
    - $0.20 per 1 million requests
- Pay per duration:
    - 400K GB-seconds of compute time for free which means 400K seconds of running time if the function has 1GB of RAM, 3.2 million of seconds if the function requires 128 MB of RAM
    - After that $1 for 600K GB-seconds
- It is usually very cheap to run AWS Lambda functions

## AWS Lambda Limits - per region

- Execution:
    - Memory allocation: 128 MB - 3008 MB (64 MB increments)
    - Maximum execution time: 900 seconds (15 minutes)
    - Environment variables: 4 KB
    - Disk capacity in the function container (`/tmp`): 512 MB
    - Concurrent executions: 1000 per account (can be increased after a request)
- Deployment:
    - Lambda function deployment size (compressed.zip): 50 MB
    - Uncrompressed deployment size: 250 MB
    - We can use `/tmp` directory to load other files at the startup

## AWS Lambda@Edge

- Used for running global Lambda functions alongside edge locations (for CDN for example)
- Can be used for:
    - To change CLoudFront requests and responses:
        - After CloudFront receives a request from a  viewer (viewer request)
        - Before CloudFront receives the request from the origin (origin request)
        - After CloudFront receives the response from the origin (origin response)
        - Before CloudFront forwards the response to the viewer (viewer response)
    - Customize the CDN content
- Use cases:
    - Website security and privacy
    - Dynamic Web Application at the Edge
    - Search Engine Optimization (SEO)
    - Intelligent Route across origins and data centers
    - Bot mitigation at the Edge
    - Real-time image transformation
    - A/B testing
    - User authentication and authorization
    - User prioritization
    - User tracking and analytics

