# Elastic Load Balancers

## Scalability and High Availability

- Scalability means that an a system can handle greater loads by adapting
- We can distinguish two types of scalability strategies:
    - **Vertical Scalability** (scale up/down)
        - Increase the size of the current instance (ex. from a t2.micro instance migrate to a a t2.large one)
        - Vertical scalability is common for non distributed systems, such as databases
        - RDS, ElastiCache are services that can scale vertically
    - **Horizontal Scalability** (scale out/in)
        - Increase the number of instances on which the application runs
        - Horizontal scaling implies having a distributed system
        - It's easy to scale horizontally thanks to could offerings such as EC2
- High Availability means running our application in at least 2 data centers (AZs)
    - The goal of high availability is to survive a data center loss
- High Availability can be:
    - Passive
    - Active
- Load balancers can scale but not instantaneously - contact AWS for a "warm-up"
- Troubleshooting:
    - 4xx errors are client induced errors
    - 5xx errors are application induced errors (server side errors)
    - Error 503 means that the load balancer is at capacity or no registered targets can be found
    - If the load balancer can't connect to the application, it most likely means that the security group blocks the connection
- Monitoring:
    - ELB access logs will log all the access requests to the LB
    - CloudWatch Metrics will give aggregate statistics (example: connections counts)

## Load Balancing Basics

- Load balancers are servers that forward internet traffic to multiple other servers (most likely EC2 instances)
- Why use load balances?
    - Spear load across multiple downstream instances
    - Expose a single point of access (DNS) to the application
    - Seamlessly handle failures of downstream instances (by using health checks)
    - Do regular health checks to registered instances
    - Provide SSL termination (HTTPS) for the website hosted on the downstream instances
    - Enforce stickiness for cookies
    - High availability across availability zones (load balancer can be spread across multiple AZs, not regions!!!)
    - Cleanly separate public traffic from private traffic
- An ELB (Elastic Load Balancer) is a **managed load balancer** which means:
    - AWS guarantees that it will be working
    - AWS takes care of upgrades, maintenance and high availability
    - An ELB provides a few configuration options for us also
    - It costs less to setup our custom load balancer, but it will be a lot more effort to maintain on the long run
    - An ELB is integrated with many AWS offering/services, it will be more flexible than a custom LB

## Health Checks

- They enable for a LB to know if an instance for which traffic is forwarded is available to reply to requests
- The health checks is done using a port and a route (usually /health)
- If the response is not 200, then the instance is considered unhealthy

## Types of Load Balancers on AWS

- AWS provides 4 type of load balancers:
    - Classic Load Balancer (v1 - old generation): supports HTTP, HTTPS and TCP
    - Application Load Balancer (v2 - new generation): supports HTTP, HTTPS and WebSockets
    - Network Load Balancer (v2 - new generation): supports TCP, TLS (secure TCP) and UDP
    - Gateway Load Balancer (new generation - see VPC section of the notes)
- It is recommended to use the new versions
- We can setup **internal** (private) and **external** (public) load balancers on AWS

### Classic Load Balancers (CLB)

- They support 2 types of connections: TCP (layer 4) and HTTP(S) (layer 7)
- Health checks are either TCP or HTTP based
- CLBs provide a fixed hostname: XXX.region.elb.amazonaws.com

### Application Load Balancers (ALB)

- They are a layer 7 type load balancers (only HTTP or HTTPS)
- They allow load balancing to multiple HTTP applications across multiple machines (target groups). Also they allow to load balance to multiple applications on the same EC2 instance (useful in case of containers)
- They have support for HTTP2 and WebSockets.
- They support redirects, example for HTTP to HTTPS
- They provide routing tables to different target groups:
    - Routing based on path in URL
    - Routing based on the hostname
    - Routing based on query strings an headers
- ALBs are great fit for micros-services and container based applications
- ALBs have port mapping features to redirect to dynamic ports in case ECS
- Target groups can contain:
    - EC2 instances (can be managed by an Auto Scaling Group)
    - ECS tasks (managed by ECS itself)
    - Lambda Functions -  HTTP request is translated to a JSON event
    - IP Addresses - must be private IP addresses
- ALBs also provide a fixed hostname (same as CLBs): XXX.region.elb.amazonaws.com
- The application servers behind the LB can not see the IP of the client who accessing them directly, but they can retrieve for **X-Forwarded-For** header. The port can be fetched from **X-Forwarded-Port** and the protocol from **X-Forwarded-Proto**

### Network Load Balancers (NLB)

- Network load balancers (layer 4) allow to:
    - Forward TCP and UDP traffic to the registered instances
    - Handle millions of requests per second
    - Less latency ~100ms (vs 400ms for ALB)
- NLBs have **one static IP per AZ** and supports Elastic IPs (can be used when whitelisting is necessary)
- Use case for NLBs: NLBs are used for extreme performance in case of TCP or UDP traffic (example: video games)
- Instances behind an NLB don't see traffic coming from the load balancer, they see traffic as it was coming from the outside world => no security group is attached to LB => security group attached to the target EC2 instance should be changed to allow traffic from the outside (example: 0.0.0.0/0, on port 80)

## Stickiness

- It is possible to implement stickiness in case of CLB and ALB load balancers
- Stickiness means that the traffic from the same client will be forwarded to the same target instance
- Stickiness works by adding a cookie to the request which has an expiration date for controlling the
stickiness period
- Possible use case for stickiness: we have to make sure that the user does not lose his session data
- Enabling stickiness may bring imbalance to the load over the downstream target instances

## Cross-Zone Load Balancing

- With Cross-Zone Load Balancing enabled each LB instance distributes traffic evenly across multiple AZs
- Otherwise, ech LB node distributes requests evenly only in the AZ where it is registered
- Classic Load Balancer: 
    - Cross-zone load balancing is disabled by default
    - No additional charges for cross zone load balancing if the feature is enabled
- Application Load Balancer: 
    - Cross-zone load balancing is always on, can not be disabled
    - No charges applied for cross zone load balancing
- Network Load Balancer:
    - Cross-zone load balancing is disabled by default
    - Additional charges apply if the feature is enabled

## SSL/TLS Certificates

- An SSL certificate allows traffic to be encrypted between the clients and the load balancers. This ia called encryption in transit or in-flight encryption
- SSL - Secure Socket Layer
- TLS (newer version of SSL) - Transport Layer Security
- Nowadays TLS are mainly used, but we are still referring to it as SSL
- Public SSL certificates are issued by a Certificate Authority
- SSL certificates have an expiration dates and they must be renewed
- SSL termination: client can talk with a LB using HTTPS but internal traffic can be routed to a target using HTTP
- Load balancer can load an X.509 certificate (which is a SSL/TLS server certificate)
- We can manage certificates in AWS using ACM (AWS Certificate Manager)
- HTTPS Listener:
    - We must specify a default certificate
    - We can add an optional list of certificates to support multiple domains
    - Clients can use SNI (Server Name Indication) to specify which hostname want to reach
    - Ability to specify a security policy to support older versions of SSL/TLS (for legacy clients like Internet Explorer 5 lol:) )

### SNI - Server Name Indication

- SNI solves the problem of being able to load multiple SSL certificates onto one web server
- There is a newer protocol which requires the client to indicate the hostname of the target server in the initial SSL handshake
    - In case of AWS this only works for ALB, NLB and CloudFront (no CLB!)

## ELB - Connection Draining

- Feature naming:
    - In case of a CLB is called Connections Draining
    - If we have a target group: (ALB, NLB) it is called Deregistration Delay
- Connection draining is the time to complete in-flight requests while the instance is de-registering or unhealthy. Basically it allows the instance to terminate whatever it was doing
- The LB will stop sending new requests to the target instance which is in progress of de-registering
- The time period of the connection draining can be set between 1 seconds to 3600 seconds
- It also can be disabled (set the period to 0 seconds)
