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

## VPC Peering

