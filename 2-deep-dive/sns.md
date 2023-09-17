# SNS - Simple Notification Service

- Pub/Sub model
- The event produces only sends messages to one SNS topic
- Each subscriber to the topic will get all the messages be default (we can filter them, if we want)
- We can have up to 10 million subscribers per topic
- We cave up to 100K topics
- Subscribers to the topic can be:
    - SQS
    - HTTP/HTTPS
    - Lambda
    - Emails
    - SMS messages
    - Mobile Notifications
- Many different services integrate with SNS for notifications, for example:
    - CloudWatch (for alarms)
    - Auto Scaling Groups notifications
    - S3 (bucket events)
    - CloudFormation (state changes)
    - Etc...
- How to publish?
    - In order to publish we must create a topic using the SDK
    - We may create one or many subscriptions
    - We publish data to the topic
- Direct Publish (for mobile apps SDK)
    - Create a platform application
    - Create a platform endpoint
    - Publish to the platform endpoint
- Direct Publish works with Google GCM, Apple APNS, Amazon ADM

## Security

- Encryption:
    - In-flight encryption using HTTPS API
    - At-rest encryption using the KMS keys
    - Client-side encryption if the client wants to perform encryption/decryption itself
- Access Controls: IAM policies to regulate access to the SNS API
- SNS Access Policies (similar to S3 bucket policies):
    - Useful for cross-account access to SNS topics
    - Useful for allowing other services (S3) to write to an SNS topic

## SNS + SQS Fan Out

- Send a message to multiple SQS queues using SNS
- Push one in SNS, receive in all SQS queues which are subscribers
- Fully decouples, no data loss
- SQS allows for data persistance, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- SQS queues must have an allow access policy for SNS to be able to write to the queues
- **SNS cannot send messages to SQS FIFO queues (AWS limitation)!**
- Use case: send S3 events to multiple queues:
    - For the same combination of even type and prefix we can only have one S3 Event rule
    - In case we want to send the same S3 event to many SQS queues, we must use SNS fan-out
