# AWS CLI, SDK, IAM Roles and Policies

- Performing tasks against AWS can be done in several ways
    - Using AWS CLI from a local machine
    - Using AWS CLI on an EC2 machine
    - Using the SDK from a local machine
    - Using the SDK from an EC2 instance
    - Using the AWS Instance Metadata Service for EC2

## AWS CLI Configuration

- In order to use the CLI from a local machine, we must generate access keys. Access keys can be generated from AWS console IAM service
- Access keys are provided as .csv file and they can be downloaded only once
- Set up AWS CLI from terminal:
    ```
    aws configure
    ```
- This command creates 2 files in `/.aws/config` folder: `config` and `credentials`
- A configuration can be invalidated by deleting the access keys or it can be inactivated

## AWS CLI on EC2

### BAD WAY - Don't do this

- **Never ever put personal credentials on an EC2 instance!**
- This means, never put secrets in when running `aws configure` command. Use this for setting some defaults, like region and output format

### THE RIGHT WAY

- IAM Roles can be attached to EC2 instances
- IAM Roles can come with a policy authorizing exactly what the EC2 instance should be able to do
- This is the best practice on AWS and should be done every time!

## IAM Roles and Policies

- Policies can be managed by AWS or custom managed by users
- AWS provides a huge set of managed policies, if these are not good enough the users can create their own
- Inline policies: policies that are added inline to a role, this make them impossible to add them to another role
- AWS Policy generator: https://awspolicygen.s3.amazonaws.com/policygen.html
- AWS Policy simulator: https://policysim.aws.amazon.com/

## EC2 Instance Metadata

- It allows EC2 instance to "lear about themselves" without using an IAM Role
- The URL to get EC2 metadata information is http://169.254.169.254/latest/meta-data. THis URL only works from EC2 instances, since it is an internal IP
- We can retrieve the IAM Role name for the EC2 instance but we can not retrieve the IAM Policy
- Metadata = info about the EC2 instance
- Userdata = launch scripts on the EC2 instance initial startup

## AWS SDK

- SDK = Software Development Kit
- Official SDKs are for:
    - Java
    - .NET
    - NodeJS
    - PHP
    - Python (Boto3)
    - Go
    - Ruby
    - C++
- AWS CLI uses Boto3 under the

### AWS SDK Credentials Security

- It is recommended to use the **default credential provider chain**
- The default credential provider chain works seamlessly with:
    - AWS credentials at `~/.aws/credentials` (only on our computers or on premise)
    - Instance Profile Credentials using IAM Roles (for EC2 machines, etc.)
    - Environment variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
- Never store AWS credentials in code!
- Best practice is to inherit credentials from the credential provider chain

### Exponential Back-off

- Any API that fails because of too many requests needs to be retried with Exponential Back-off strategy
- These applies for rate limited APIs
- SDK usually implements exponential back-off out of the box for requests
- Exponential Back-off example:
    1. First API call fails. After failure we wait 1s and retry
    2. Second API call fails. We wait 2s
    3. Third API call fails. We wait 4s
    4. Forth API call fails. We wait 8s
    5. Etc..until the request succeeds
- Exponential back-off ensures the API is not overloaded



