# Networking - VPC

## CIDR - Classless Inter-Domain Routing

- CIDR is used for Security Group rules and AWS networking in general
- It is used to define an IP address range
    - ww.xx.yy.zz/32 - one IP address
    - 0.0.0.0/0 - all possible IP addresses
    - 192.168.0.0/26 - range of 192.168.0.0 - 192.168.0.63 64 IP addresses
- CIDR has to component:
    - The base IP: represents an IP from a given the range
    - The subnet mask: defines how many bits can change in the IP
- The subnet mask can take 2 forms:
    - 255.255.255.0 - less common
    - /24 - more common
- The subnet mask basically allows a part of the base IP to get additional next values:
    - /32 allows for 1 IP => 2^0
    - /31 allows for 2 IPS => 2^1
    - /30 allows for 4 IPs => 2^2
    - /29 allows for 8 IPs => 2^3
    ...
    - /0 allows for 2^31 IPs, all IPs
- Most used subnet masks:
    - /32 - no IP number can change
    - /24 - last IP number can change (256 IP addresses)
    - /16 - last 2 IP numbers can change (65536 IP addresses)
    - /8 - last three IP numbers can change
    - /0 - all the IP numbers can change
- To calculate CIDR, use: https://www.ipaddressguide.com/cidr

## Private vs Public IP Addresses (IPv4)

- Private IP can only allow certain values:
    - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) - used for big networks
    - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12) - default AWS private VPC
    - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) - home networks
- All the rest of the IP addresses are considered public

## Default VPC

- All new accounts have a default VPC
- New instances are launched into default VPC if no subnet is specified
- Default VPCs have internet connectivity and all instances have public IP address
- We also get a public and a private DNS name

## VPC - Virtual Private Cloud

- We can have multiple VPCs in a region (max 5 per region - soft limit)
- Max CIDR per VPC is 5, for each CIDR:
    - Min size is /28 = 16 IP addresses
    - Max size is /16 = 65K IP addresses
- VPC is private => only the private address ranges are allowed
- **A newly created VPC CIDR should not overlap with an existing one used another VPC**


## VPC Subnets

- Subnets are tied to specific AZs
- AWS reservers 5 IP addresses (first 4 and the last 1 address) in each subnet
- These 5 IP addresses are not available and can not be assigned to an instance
- Example, in case of a CIDR block 10.0.0.0/24 the reserver IPs are the following:
    - 10.0.0.0 - Network address
    - 10.0.0.1 - Reserved by AWS for the VPC router
    - 10.0.0.2 - Reserved by AWS for mapping to an Amazon provided DNS
    - 10.0.0.3 - Reserved for future use
    - 10.0.0.255 - Network broadcast address. AWS does not supports broadcast in a VPC, therefore the address is reserved

## IGW - Internet Gateways

- Internet gateways helps a VPC instance to connect to the internet
- It scales horizontally and is HA and redundant
- Must be created separately from a VPC
- One VPC can have only one attached internet gateway, one internet gateway can have only one VPC
- Internet Gateway is also a NAT for the instances that have a public IPv4
- Internet Gateways on their one do not allow internet access, route tables also must be configured

## NAT Instances - Network Address Translation (outdated)

- Allow instances in private subnets to connect to the internet
- NAT instances must be launched in a public subnet to have internet access
- *Source/Destination Check* flag for EC2 must be disabled
- NAT instanced should have an Elastic IP attached to them
- Route tables must be configured to route traffic from the private subnet to the NAT instance
- In order to set up we must use a pre-configured Amazon Linux AMI
- It is not HA/resilient out of the box => We would need to set up an ASG in multi AZ + resilient user data script
- Internet traffic bandwidth depends on the EC2 instance performance

## NAT Gateway

- AWS managed NAT, higher bandwidth, better availability, no administration
- It is pay by hour for usage and bandwidth
- NAT is created in a specific AZ and it uses an Elastic IP
- It can not be used by an instance from the same subnet as the NAT
- Required an IGW
- 5Gbps of bandwidth with automatic scaling up to 45Gbps
- There is no security group to manage
- NAT Gateway is resilient within a single AZ
- We must create multiple NAT Gateways in multiple AZs for fault-tolerance

## Comparison between NAT Instance and NAT Gateway

- AWS comparison: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html

## DNS Resolution in VPC

-DNS Resolutions settings:
    - **enableDnsSupport**:
        - By default is set to true
        - Helps decide if DNS resolution is supported for the VPC
        - If true, queries the AWS DNS server at 169.254.169.253
    - **enableDNSHostName**:
        - By default is set to false for an user created VPC, true for the Default VPC
        - Won't do anything unless enableDnsSupport=true
        - If true, the VPC assigns public host names to EC2 instances
- If we use custom DNS domain names in a private zone in Route53, we must set both of these attributes to true

## Network Access Control Lists (NACL) and Security Groups (SG)

- NACL is a like a firewall which controls traffic which goes outside of comes inside a subnet
- The default NACL allows everything outbound and everything inbound
- We define one NACL per subnet, new subnets are assigned the default NACL
- We can define NACL rules:
    - Rules have a number (1 - 32766) which defines the precedence
    - Rules with lower number have higher precedence
    - Rule with higher precedence wins at the time of evaluation
    - Last rule is has the precedence of asterisk (*) and denies all the requests. This rule is not editable
    - AWS recommends adding rules by increment of 100
- Newly created created NACL will deny everything (last rule)
- NACLs are great way of blocking a specific IP address at the subnet level

| Security Group                                           |  Network ACL                                                            |
|----------------------------------------------------------|-------------------------------------------------------------------------|
| Operates at the instance level                           | Operates at the subnet level                                            |
| Supports allow rules only                                | Supports allow rules and deny rules                                     |
| It is stateful: return traffic is automatically allowed  | It is stateless: return traffic must be explicitly allowed by the rules |
| All rules are evaluated before deciding to allow traffic | Rules have a precedence when deciding whether to allow or deny traffic  |
| It is associated to an instance inside of a VPC          | Automatically applies to all instances in the subnet                    |

- Example of NACL with ephemeral ports: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html

## VPC Peering

- VPC Peering allows to connect two VPCs privately using AWS network. This will make them behave as if they were the same network
- Networks must have non-overlapping CIDRs
- VPC Peering connections is not transitive, which means it must be established for each VPC that needs to communicate with other
- VPC Peering can be done form one AWS account to another
- We must update the route tables in each VPC subnet to ensure instances can communicate with each other
- VPC peering can work inter-region, cross-account. We can reference a security group of a peered VPC (this works cross account also)


## VPC Endpoints

- Allow connection to AWS services from VPCs using a private network instead of the public internet
- They scale horizontally
- They remove the need of IGW, NAT, etc. to access AWS services
- There are 2 kinds of VPC Endpoints:
    - **Interface**: provisions an ENI (private IP address) as an entry point. A security group must be attached to it. Most AWS services use this method
    - **Gateway**: provisions a target and must be used in a route table. Gateway is used for S3 and DynamoDB
- In case of issues:
    - We have to check DNS settings resolution in the VPC
    - We have to check the route tables

## VPC FLow Logs

- Flow logs help capture information about the IP traffic going into the interfaces
- There are 2 kinds of Flow Logs:
    - VPC Flow Logs
    - Subnet Flow Logs
    - Elastic Network Interface Flow Logs
- Flow logs help monitor and troubleshoot connectivity issues
- Flow logs data can go to S3/CloudWatch logs
- It can capture network information from some of the AWS managed interface too: ELB, RDS, ElastiCache, Redshift, AWS Workspaces

### Flow Log Syntax

```
<version> <account-id> <interface-id> <srcaddr> <dstaddr> <srcport> <dstport> <protocol> <packets> <bytes> <start> <end> <action> <log-status>
```
- Srcaddr, dstaddr help identify problematic IP addresses
- Scrport, dstport help identify problematic ports
- Action: success or failure of the request due to SG/NACL
- Flow logs can be used for analytics on usage patterns or malicious behavior
- We can query VPC flow logs using Athena on S3 or CloudWatch Logs Insights

## Bastion Hosts

- Bastion hosts are used to SSH into instances from private subnets
- The bastion host is sitting in the public subnet which is connected to the private subnets
- Bastion host SG must be really strict
- We have to make sure the bastion host only has port 22 traffic from the IP we need, not from the security group of other instances

## Site to Site VPN

- Virtual Private Gateway:
    - VPN concentrator on the AWS side of the VPN connection
    - Virtual Private Gateway (VPG) is created and attached to the VPC from which we want to create the site-to-site VPN connection
    - Possibility to customize the ASN
- Customer Gateway:
    - Software application or physical device on customer side of the VPN connection
    - IP Address: use static, internet-routable IP address for the customer device. If it is behind a NAT (with NAT-T enabled), use the public IP address of the NAT

## Direct Connect (DX)

- Direct Connect provides a dedicated private connection from a remote network to the AWS VPC
- Dedicated connection must be setup between the client's DC and AWS Direct Connect locations
- In AWS we still need to setup a Virtual Private Gateway in the VPC
- DX will allow access to public resources (S3) and private (EC2) on the same connection
- Use cases:
    - Increase bandwidth throughput
    - More consistent network experience - application using real-time data feeds
    - Hybrid Environment
- DX supports both IPv4 and IPv6
- Direct Connect Gateway: if we want to setup a Direct Connect to one or more VPC in many different regions (but in the same account), we must use a Direct Connect Gateway
- Direct Connect Gateway does not replace VPC Peering
- Direct Connect has two connections types:
    - Dedicated Connections: 1 Gbps and 10 Gbps capacity
        - Physical ethernet port dedicated to a customer
        - In order to get this a request has to be made to AWS first, then completed by AWS Direct Connect Partners
    - Hosted Connections: 50 Mbps, 500 Mbps, up to 10 GBps
        - Connection requests are made via AWS Direct Connect Partners
        - Capacity can be added or removed on demand
- Lead times are often longer than 1 month

### Direct Connect Encryption

- Data in transit is not encrypted but is private
- In case there is need for encryption we can combine DX with VPN which provides IPsec-encrypted private connection

## Egress Only Internet Gateway

- Egress only Internet Gateway works only for IPv6 
- Similar function as a NAT, but for IPv6 (NAT is for IPv4)
- All IPv6 addresses are public addresses, therefore all our instances are publicly accessible
- Egress Only Internet Gateway gives our IPv6 instances access to the internet, but they wont be directly reachable by the internet
- After creating an Egress Only Internet Gateway we have to edit the route tables

## Exposing Services in a VPC to Other VPC

- Option 1: make it public
    - Goes through the public internet
    - Tough to manage access
- Option 2: VPC peering
    - Must create many peering relations
    - Opens the whole internal network to the other network
- Option 3: AWS Private Link (VPC Endpoint Services)
    - Most secure and scalable way to expose a service to multiple other VPCs
    - The solution does not require VPC peering, IGW, NAT, route tables
    - Requires a network load balancer in the service VPC and an ENI in the customer VPC

## EC2-Classic and AWS ClassicLink (deprecated)

- EC2-Classic: instances are running in a single network shared with other customers
- Amazon VPC: instances are running logically isolated in an AWS account
- ClassicLink allows to link EC2-Classic instances to a VPC inside an account. In order to have a link between a VPC and an EC2-Classic instance we have to:
    - Create a ClassicLink and associate a security group to it which will enable private communication
    - ClassicLink removes the necessity to have IPv4 public addresses for this type of communication

## AWS VPN CloudHub

- Provides secure communication between multiple customer networks in case we have multiple VPN connections
- Low cost hub-and-spoke model for primary or secondary network connectivity between locations

## Transit Gateway

- It is a star connection between all the VPCs and the on-premises infrastructure
- It is for having transitive peering between thousands of VPCs and on-premises and hub-and-spoke connections
- Transit Gateway is a regional resource, but it can work cross-region
- It supports cross-account sharing using Resource Access Manager (RAM)
- We can peer Transit Gateways across regions
- Route tables can limit which VPC can talk with what other VPC
- Works with Direct Connect Gateway, VPN connections
- Supports **IP Multicast** (not supported by any other AWS services)

## VPC Summary

- CIDR: IP Range
- VPC: Virtual Private Cloud => we define a list of IPv4 and IPv6 CIDRs
- Subnets: tied to an AZ, we define it by CIDRs
- Internet Gateway: at the VPC level, provides IPv4 and IPv6 internet access
- Route tables: must be edited to add routes from subnets to the IGW, VPC peering connections, VPC Endpoints, etc.
- NAT Instance: gives internet access to instances in a private subnets. Deprecated, must be setup in a public subnet, Source/Destination flag should be disabled
- NAT Gateway: managed by AWS, provides scalable internet access to private instances, only for IPv4
- Private DNS + Route 53: enables DND Resolution + DNS hostname inside a VPC
- NACL: Stateless, subnet level routes, used for filter inbound and outbound traffic
- Security Groups: Stateful, operate at the instance level
- VPC Peering: connect to VPC with non overlapping CIDRs, non transitive
- VPC Endpoints: provide private access to AWS Services
- VPC FLow Logs: can be configured at VPC/Subnet/ENI level, used for monitoring traffic
- Bastion Hosts: public instances to SSH into, used for connecting to other private instances via SSH
- Site to Site VPN: used for setup a Customer Gateway on DC, a Virtual Private Gateway on VPC and site-to-site VPN over public internet
- Direct Connect: used for setup a VIrtual Private Gateway on VPC and establish a direct private connection to an AWS Direct Connect Location
- Direct Connect Gateway: setup Direct Connect to many VPCs in different regions
- Internet Gateway Egress: similar to NAT, but for IPv6
- Private Link (VPC Endpoint Services): connect services from one VPC to another
- ClassicLink: connect EC2-Classic instances to VPCs
- VPN CloudHub: hub-and-spoke VPN model to connect sites
- Transit Gateway: transitive peering connection for VPC, VPN and DX