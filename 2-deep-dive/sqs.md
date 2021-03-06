# SQS - Simple Queue Service

## Integration and Messaging

- Deploying multiple applications will inevitable result in the necessity of a communication layer between them
- There are 2 types of integration communication patterns:
    - Synchronous communication
    - Asynchronous communication
- Application decoupling models:
    - SQS: queue model
    - SNS: pub/sub model
    - Kinesis: real-time streaming model

## SQS - Standard Queue

- Oldest offering on AWS (over 10 years old)
- Fully managed service, used to decouple applications
- Attributes:
    - Unlimited throughput, unlimited number of messages in the queue
    - Each message is short leaved: default retention period is 4 days, maximum is 14 days
    - Low latency: <10 ms on publish and receive
    - Limitation for message size: maximum size of a message is 256KB
- SQS Standard Queue can have duplicate messages (at least once delivery)
- It also can have out of order messages (best effort ordering)

### SQS Standard -  Producing Messages

- Producers send messages to the queue using the SDK (SendMessage API)
- The message is persisted on the queue until a consumer deletes it
- Message retention: default 4 days, up to 14 days
- SQS standard has unlimited throughput

### SQS Standard - Consuming messages

- Consumers are applications (running on EC2 instances, other servers or AWS Lambda)
- Consumers poll the queue for messages (they can receive up to 10 messages at a time)
- After the messages are processed the consumers delete the messages from the queue using DeleteMessage API
- Multiple consumers:
    - Consumers receive the messages in parallel
    - Each consumer consumes a fraction of the number of the messages sent
    - We can scale the number of the consumers based on the throughput of processing
- SQS with Auto Scaling Group:
    - We can scale based on the **ApproximateNumberOfMessages** metric by creating a CloudWatch alarm

## SQS Security

- Encryption:
    - In-flight encryption using HTTPS
    - At-rest encryption using KMS
    - Client-side encryption if the client wants to perform encryption/decryption itself
- Access Control: IAM policies to regulate access to the SQS API
- SQS Access Policies:
    - Useful for cross-account access to SQS queues
    - Useful for allowing other services (SNS, S3) to write to an SQS queue

## Message Visibility Timeout

- After a message is polled by a consumer, it becomes invisible to other consumers
- Default message visibility timeout is 30 seconds, which means the consumer has 30 seconds to process the message
- After the message visibility timeout is over, the message becomes visible to other consumers
- If the processing is not finished during the visibility timeout, there is a chance the message will be processed twice
- If a consumer knows that the processing wont finish in time, it can use the **ChangeVisibility** API to request more time
- If the message visibility timeout is high and the processing fails, it may take a long time for the message to be processed again
- If the visibility timeout it too short, we may end up processing the same message twice
- Best practice: the visibility timeout should be set to something appropriate. The consumer must be implemented in a way to use the ChangeVisibility API

## Dead Letter Queues

- If a consumer fails to process a message within the visibility timeout, the messages goes back to the queue. This can happen multiple times.
- We can set a **MaximumReceives** threshold, which denotes how many time a message should be able to go back to the queue
- If the MaximumReceives threshold is exceeded, the message is sent to a dead letter queue
- DLQs are useful for debugging
- We have to make sure the messages are processed in DLQ before expiring. It is not a good idea to set a short expiration time for the DLQ

## Delay Queue

- Delaying a message means the consumers wont be able to see the message for a period of time after it was sent. Delay time can be up to 15 minutes
- Delay can be set at a queue level or tt also can be set to message level using the **DelaySeconds** parameter

## FIFO Queues

- FIFO - First In First Out
- The messages will be ordered in the queue, meaning that the messages will be consumed in the same order as they were sent
- FIFO queues have limited throughput: 300 msg/s without batching, 3000 msg/s with batching
- Exactly-once send capability (by activating content-based deduplication)
- The name of the FIFO queue must end with the `.fifo`

## SQS With Auto Scaling Group

- Allows scaling the number of EC2 instances based on the available messages in the queue
- In order to accomplish auto scaling we have to create a CloudWatch custom metric representing the number of available messages on the queue divided by the number of EC2 instances. This metric is pushed from an EC2 instance

## Data Ordering in SQS

- For standard SQS queues there is no data ordering
- For SQS FIFO, if we don't use a Group ID, messages are consumed in the order they are sent, with **only one consumer**
- Messages can be group by specifying a Group ID for the message
- For each group we can have different consumers, which will read messages in order

