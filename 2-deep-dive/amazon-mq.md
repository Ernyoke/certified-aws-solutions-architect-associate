# Amazon MQ

- SQS and SNS are cloud-native, they are using proprietary protocols from AWS
- Traditional application running on on-premise may use queues with open protocols such as: MQTT, AMQP, STOMP, Openwire, WSS
- When migrating to cloud instead of re-engineering the application to SQS or SNS, we can use Amazon MQ
- Amazon MQ is basically managed Apache ActiveMQ
- Amazon MQ does not scale as much as SQS/SNS
- It runs on a dedicated machine, can urn in HA with failover
- It has both queue anf topic features
