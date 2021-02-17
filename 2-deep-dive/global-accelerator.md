# Global Accelerator

## Unicast IP vs Anycast IP

- Unicast IP: one server holds one IP address
- Anycast IP: more than one servers are holding the same IP address, the client will be routed to the nearest one

## AWS Global Accelerator 

- It leverages the AWS internal network to route traffic to our application
- 2 Anycast IPs are created for our application, which are global. The traffic from these IPs is sent directly to Edge Locations which will send it to the application using the AWS global network
- Global Accelerator works with Elastic IP, EC2 instances, ALB, NLB which can either be public or private
- It provides consistent performance by:
    - Intelligent routing to the lowest latency and fast regional failover
    - Not having to deal with client cache (because the IP does not change)
    - It is internal to the AWS network
- Health checks
    - Global Accelerator performs health checks to our applications
    - Failover is less than 1 minutes in case of unhealthy application instance
    - Great for disaster recovery
- Security
    - It provides 2 external IPs which may be needed to be whitelisted
    - DDoS protection thanks to AWS Shield

## AWS Global Accelerator vs CloudFront

- The both use the same AWS global network and its edge locations around the world
- Both services integrate with AWS Shield for DDoS protection
- CloudFront:
    - Improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery)
    - Content is served from the edge locations
- Global Accelerator
    - Improves performance for a wide range of applications over TCP or UDP
    - Packages to the application are proxied from the edge locations
    - Good fit for non-HTTP applications such as gaming (UDP), IoT (MQTT) or Voice over IP
    - Good fir tor HTTP in case if it is required to have static IP addresses or deterministic and fast regional failover