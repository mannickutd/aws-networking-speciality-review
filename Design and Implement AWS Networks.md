# Design and Implement AWS Networks

## AWS Global infrastructure

| Infrastructure | Role | Notes |
| -------------- | ---- | ----- |
| Regions | Provide geographical locations in the world where AWS operates services. | Regions are completely independent of other regions. |
| Availability zones | Provides a fast connection to other zones in the same region. | Consists of one or more data centers within a region. AZ's have 'a', 'b', 'c' appended onto them. |
| Edge locations | Deliver content to end users with low latency. | A content distribution network where data is cached. The backbone of cloud front. |

## VPC Basic Networking Design

VPC - A VPC lets you provision a logically-isolated section of the AWS cloud. You have complete control over your virtual networking environment, selection of IP address range, creation of subnets, and configuration of routing tables and network gateways.

IPv4 Number of IPv4 address
2^(32 - slashSubnetting)
EG. Number of IPv4 address in /24?
2^(32-24) = 2^8 = 256

** NOTE: ** AWS will take 5 IP addresses for its own use whenever a VPC is created.

Steps to create a VPC:
* Choose a IPv4 CIDR range.
* Choose a tenancy.
* Optionally associate a IPv6 CIDR range.

| IPv4 Addressing | IPv6 Addressing |
| --------------- | --------------- |
| Required for all VPCs | Optional |
| You have a choice of size between /16 to /28 CIDR block. | Has a fixed size of /56 CIDR block. |
| Public and private addressing is a thing. | There is no difference between public and private addresses. Security is controlled with routing and security policies. |

** NOTE: ** IPv4 addresses are assigned to every resource in your VPC, regardless of whether you use IPv4 for communications. Because of this the number of useable IPv6 addresses in your VPC is constrained by the number of available IPv4 addresses.

** NOTE: ** The default VPC in every region, in every AZ, has a default IPv4 CIDR range of 172.31.0.0/16.

## VPC, Subnets, CIDR Blocks

VPC Router - A logical construct where all routing decisions begin. Routing decisions are governed by route tables associated with VPCs and subnets. This is the first place a packet will hit when leaving a resource.

AWS Reserved Network Addresses:
* 10.0.64.0 - Network Address
* 10.0.64.1 - Reserved by AWS for the VPC router.
* 10.0.64.2 - The IP address of the DNS server.
* 10.0.64.3 - Reserved by AWS for future use.
* 10.0.71.255 - Network broadcast address.

## Elastic Network Interfaces, Elastic IPs, and Internet Gateways

ENI - A virtual network interface that you can attach to an instance of a VPC. Upon creation, ENIs are inside a VPC and are associated with a subnet.
* Primary IPv4 address.
* Mac address.
* At least one security group.

Attributes of an ENI:
* MAC addresss.
* Internal IP addresses.
* Auto-assigned external IP.
* Elastic external IP.
* Source/dest check.
* Security Groups.

** NOTE: ** You cannot add more ENIs to a resouce to increase/improve the network bandwidth (also called NIC teaming).

Internet Gatway (IGW) - A highly scalable VPC component that allows communication with resources in your VPC and the internet. When an instance recieves traffic from the internet the IGW translates the destination address to the instances private address and forwards the traffic to the VPC.

Elastic IPs (EIP) - A static, public IPv4 address that you can attach to your account (taken from a pool) and release from your account (returned to the pool). The address comes from a pool of regional IPv4 addresses that AWS manages.

| Elastic IP | Dynamic External IP |
| ---------- | ------------------- |
| You request allocate from IPv4 address pool. | Assigned upon EC2 creation. |
| Assign EIP to an EC2 instance (technically assigned to the underlying ENI). | Cannot disassociate address from EC2 instance after launch. |
| You choose when to release back to AWS. | Automatically released when you stop or terminate the instance |

## Traffic Control: Network Access Control Lists and Security Groups

Security Groups:
* When you have have multiple security groups attached to a resource, the product of those rules is applied.
* Security groups are attached to the ENI associated with the resource.

| Security Groups | Network Access Control Lists |
| --------------- | ---------------------------- |
| Applied at the resource level (applied to ENI) | Applied at the subnet level |
| Resources can have multiple SGs. The product of the rules is applied. | The subnet is associated with one NACL, an NACL can be associated with multiple subnets |
| Stateful, return traffic is allowed with no exceptions to the rules. | Stateless, return traffice must be explicitly allowed by rules |

NACLs and rule ordering

NACL attributes:
* There is always a mandatory deny all rule.
* Rules are evaluated in order from lowest to highest.
* When a rule is encountered and it matches the request, the allow or deny action is implemented.
* The rule consists of Rule No., Type, Protocol, Port range, Source, Deny/Allow 

Ephemeral Port Ranges:
* Linux: 32768 - 61000
* Elastic load balancer: 32768 - 61000
* Windows Server 2003: 1025 - 5000
* Windows Server 2008+: 49152 - 65535
* NAT Gateway: 1024 - 65535

NAT Gateways

A NAT gateway is a Network Address Translation (NAT) service. You can use a NAT gateway so that instances in a private subnet can connect to services outside your VPC but external services cannot initiate a connection with those instances.

Internet Gateway vs Nat Gateway
* Internet Gateway (IGW) allows instances with public IPs to access the internet.
* NAT Gateway (NGW) allows instances with no public IPs to access the internet.

## VPC Endpoints

Two Types of VPC endpoints

| Interface Endpoints | Gateway Endpoints |
| ------------------- | ----------------- |
| Powered by AWS Private Link. | Gateway that can specify as a target in our route table. |
| An Elastic Network Inteface with a private IP address. | S3 and DynamoDB are supported. |
| Serves as an entry point for traffic. |  |
| Many services are supported. |  |

VPC Endpoint key points:
* Endpoints are a regional service. You cannot create a VPC endpoint for a VPC in a different region than where the service (S3) is located.
* VPC boundaries. Endpoints are not extendable across VPC boundaries. They cannot be accessed from outside a VPC or from another VPC.
* DNS resolution is required. DNS resolution is needed within a VPC. The internal VPC DNS redirects requests to the VPC endpoint.
* Default VPC policy endpoint. By default the VPC endpoint is unrestricted but can be further locked down. VPC policies do not override resource specific policies (eg. S3 bucket policy).
* Controlling access. Controlling access to VPC endpoints via NACLs can be problematic. The preferred approach is SGs, you can reference logical networking objects (eg. the VPC endpoint).
* You can have multiple VPC endpoints within the same VPC, even for the same service. Each endpoint can have its own policy and can be applied to different subnets.

## VPC Peering

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IPv4 addresses or IPv6 addresses. Instances in either VPC can communicate with each other as if they are within the same network. You can create a VPC peering connection between your own VPCs, or with a VPC in another AWS account. The VPCs can be in different Regions (also known as an inter-Region VPC peering connection).

Software VPN Endpoint Limitations:
* Compute costs will add to the cost. You will pay for the EC2 cost and specific software licences for VPN endpoints.
* High availability will add to the cost, to create high availability you will need multiple EC2 instances running.
* Personnel costs, there will be additional cost to pay somone to manage the additional the EC2 vpn endpoints.
* Performance issues, scaling and hardware acceleration might become a factor even for large EC2 instances.
* Security groups only work up until the VPN endpoint.
* DNS names, there could be issues for resolving IPs on internal networks.

## VPC Flow logs

Flow logs may be defined for VPCs, subnets, or ENIs. VPC flow logs capture information about IP traffic sessions processed by ENIs in your VPC.

VPC Flow Logs Limitations:
* Data reporting is not real time.
* Definitions cannot be modified after creation.
* May not record "original" or "correct" IP addresses.
* Does not capture all IP traffic. Ignored traffic includes:
  * EC2 DNS requests to Route53.
  * Amazon windows licence activation.
  * Instance metadata.
  * Amazon time sync service.
  * DHCP traffic.
  * Default VPC router.
  * Endpoint services.
* Does not capture application data.

AWS Default Flow Log Format V2 (always in this order)
* version
* account-id
* interface-id
* srcaddr
* dstaddr
* srcport
* dstport
* protocol
* packets
* bytes
* start
* end
* action
* log-status

V3 new fields
* vpc-id
* subnet-id
* instance-id
* tcp-flags
* type (IPv4, IPv6 or EFA)
* pkt-srcaddr
* pkt-dstaddr

V4 new fields
* region
* az-id
* sublocation-type
* sublocation-id

V5 fields
* pkt-src-aws-address
* pkt-dst-aws-address
* flow-direction
* traffic-path

## Network Performance



## Exam Tips

### AWS Global Infrastructure
* Know the difference between regions, availbility zones and edge locations.
* Understand the global infrastructure of AWS as a whole.
* Understand what resources sit inside your VPC in AWS.
* Understand what services sit outside your VPC.

### VPC and Basic Networking Design
* Know how to create VPCs and the components of a VPC. What is created by default?
* Understand what must be considered when designing VPCs in AWS.
* Understand how CIDR addressing works.
* Know the difference between IPv4 address and IPv6 in terms of how AWS manages them.
* You choose IPv4 CIDR blocks - AWS assigns the IPv6 CIDR blocks for you.
* IPv6 addresses are bound by IPv4 addresses.

### Subnets, VPC Routers and Route Tables
* Understand how subnetting works and the subnets you can create in your AWS VPCs.
* Know what to consider when choosing a CIDR block range.
* Understand the addresses AWS reserves, and what each are reserved for.
* Understand what the VPC router/implicit router is.
* Know what the main route table is and what custom route tables are.
* Be able to create local, static and dynamic routes.
* Understand that AWS predefines the route-priority process to determine how to route traffic.

### Elastic Network Interface (ENI)
* Know what Elastic Network Interfaces and what is associated with ENIs.
* Resources can have multiple ENIs attached to them. You cannot detach the primary ENI from a resource.
* Understand different scenarios where multiple ENIs would be beneficial.
* ENIs can be attached to running instances (hot attach), when stopped (warm attach) or creation (cold attach).
* Remember that SGs are associated with ENIs not EC2 instances.
* ENIs are confined to a single Availability Zone.
* NIC teaming cannot be used to increase bandwidth.

### Elastic IP Addresses
* Know what Elastic IPs are and how they differ from default public IP address.
* Understand the 1:1 relationship between private IPv4 addresses and EIPs.
* You own the EIP until you explicity release it.
* Know what you're charged for when using EIP.

### Internet Gateways (IGW)
* Know what IGWs are and the steps to ensure your subnet resources have internet access.
* Understand that the IGW is a traffic translator between public and private IP address for resources inside your public subnets.

### Security Groups
* Know what SGs are and how they are used for security.
* Understand that multiple SGs can be associated with an ENI (max 5).
* SGs have allow rules only, no deny rules.
* Understand the difference between inbound, outbound and defaults.
* Know that SGs are stateful and what that means in practice.

### NACL
* Know what NACLs are and how they are used for security.
* Every subnet must be associated with a NACL.
* NACL operate at a subnet level.
* NACL have both allow and deny rules. Understand how the priority works.

### Traffic Control: Security Groups and NACLs
* Understand how to apply SGs and NACLs in your VPC setup.
* Be able to create complex structures and security within different areas of your VPC.
* Think about the areas of your VPC that need access and do not need access.
* Understand how to diagnose a network issue that could be caused by SGs and NACLs.

### NAT Gateways
* Know what NAT gateways are and why they are recommended over NAT instances.
* NAT gateways are associated with a AZ.
* For AZ-independent architectures, you can create a NAT gateway in each AZ.
* Both NAT gateways and NAT instances are added to the route tables in the private subnets to direct inbound internet traffic to.

### VPC Endpoints
* Understand what VPC endpoints are and how they benefit your VPC setup.
* Understand the difference in gateways, VPC endpoints and interface VPC endpoints.
* Gateway VPC endpoints allow allow private access to services like S3 and dynamodb.

### VPC Peering
* Understand what VPC Peering is and why it is important.
* Understand the disadvantages of running software VPN appliances in your VPC.
* VPCs can be peered between VPCs in the same account or between other AWS accounts.
* Know how VPC peering connections are created through request/accept protocol.
* Be able to set up VPC peering and the associated routes within your VPC setups.
* Transitive peering is not supported.
* Remember you cannot create a peering connection between VPCs with overlapping or matching CIDR block ranges.

### Manage and Troubleshoot AWS Network using Flow Logs
* Understand what VPC flow logs are, how to create flow logs, and what flow logs can be attached to.
* Be able to read flow logs and know what type of information is captured.
* Remember that flow logs are not captured in real time.
* Remember that when secondary IP address of a ENI is used as a destination, the logs capture the primary IP address of the interface in the log.

### Network Performance
* Understand what options we have for launching high performance workloads in AWS.
* Know what enhanced networking is and how we can enable it on certain instance types.
* Know what cluster placement groups are.
* Remember when and where we can take advantage of 9001 MTU.
