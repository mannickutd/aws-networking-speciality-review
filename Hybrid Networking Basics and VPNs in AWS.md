### Introduction
* VPNs encrypt and isolate traffic
* Different VPN types can be used to fit demand.
* VPNs use the public internet as infrastructure.
* VPNs can be quickly created and destroyed as needed.

### Virtual Private Gateway
* Gateways
  - Internet gateway (IGW)
  - Virtual Private Gateway (VGW)
  - Transit Gateway (TGW)
* Virtual Private Gateway
  - Managed AWS service
  - Acts as a router between your VPC and non-AWS-managed networks.
  - Can be associated with multiple external connections.
  - Can attach to only one VPC at a time.
* Virtual Private Gateway
  - Site-to-Site VPN.
  - Direct Connect.
* VGW Configuration
  - Assign a name tag value.
  - Assign an ASN value.
  - Attach to a VPC.
* ASN (Autonomous System Numbers)
  - This is the Autonomous System Number (ASN) for the AWS side of a Border Gateway Protocol (BGP) session, it must be unique and cannot be the same one used for your Direct Connect or VPN.
  - Only private ASNs may be used for VGW configuration.
    1. AWS default ASN is 64512 in all regions.
    2. 7224 in most regions prior to June 30, 2018.

### AWS Hybrid Route Learning
* Static
  - Network prefixes manually configured.
  - Limited means of applying route preferences.
  - Unsuitable for larger networks.
* Dynamic
  - Peer routers share network prefixes by using a routing protocol.
  - Optional controls over route prferences.
  - Widely used in large networks.
* Site-to-Site VPN static or dynamic
* Direct Connect only dynamic
* Dynamic routing always uses Border Gateway Protocol.
* Route Propagation
  - Once turned on for a subnet, VGW adds all learned prefixes to VPC route table.
* Routing conflict logic
  - The most specific route (longest CIDR prefix) is usually preferred, but the VPC local route is always preferred over any overlapping propagated route.
  - VPC static routes take preference over matchign propagated routes.
  - If propagated routes conflict, Direct Connect > Static VPN > BGP VPN.

### Border Gateway Protocol (BGP)
* BGP allows different autonomous systems to share network route information with each other.
* BGP peering and network advertisement must be manually configured.
* BGP does not care how two peers are physically connected.
* Exterior routing protocol
* Network routes (prefixes) are shared between mutually-configured peers.
* No prefixes are automatically shared.
* Selects the "best" of multiple paths to the same destination.
* TCP 179

### VPN and IPSec Overview

### Customer Gateways

### AWS Site-to-Site VPN Configuration

### AWS VGW and VPN Limitations

## VPN Anywhere with AWS Client VPN
* Client VPN Concepts
  - Managed client-based VPN allowing secure access to AWS resources and on-premises networks.
  - AWS-managed version of the well-known OpenVPN.
  - The endpoint gets added to only one VPC. Use DNS to reference them!
  - Remember client IP CIDR cannot overlap with your target network!
  - Associate target networks for the endpoint. These are single VPC subnets.
  - Optional features, client connect handler and cloudwatch logging.
* Client Authentication
  - Active Directory
  - Mutual Authentication
  - Single Sign-On (SAML 2.0)
* Client VPN Pricing
  - Each endpoint assocication and each VPN connection.
  - Data transfer out for Amazon EC2 to internet.
  - CloudWatch logging (if enabled).
  - Client connect handler (if enabled).

### Hub-and-Spoke VPN with AWS VPN CloudHub
* AWS VPN CloudHub Concepts
  - AWS service allowing secure VPN communication from one site to another.
  - Hub-and-spoke VPN model for connecting IPSec VPNs.
  - You can use this with OR without VPC.
  - Perfect for multiple branch datacenters that need to communicate.
  - Sometimes used as a backup connection between remote offices.

### Third-Party VPN Solutions
* Why would we want to use an EC2 Based VPN?
  - Requirement for VPN protocol different from IPSec (General Routing Encapsulation or Dynamic MultiPoint VPN).
  - Anytime you need overlapping IP CIDRs.
  - Need to enable transitive routing on the AWS side of things.
  - Desire to enable special capabilities.
  - Bandwidth considerations.
  - Most common solution is via an AWS Marketplace offering.
* Things to know
  - Ensure you disable source/destination check on the VPN EC2 instance.
  - Since it is not managed, implementing recovery/failover is all on you.
  - Understand networking limits for your chosen instances.
  - Since this is not managed, you own ALL aspects of the solution.
  - Vertical scaling for increasing total performance of the EC2 instance.
  - Horizontal scaling for increasing limited bandwidth is possible as well.
 
### AWS VPN Monitoring and Optimization
* CloudWatch Metrics
  - TunnelState - 0 (Down) or 1 (Up/established)
  - TunnelDataIn - In bytes
  - TunnelDataOut - In bytes
* CloudWatch Metrics EC2 Standard Metrics
  - NetworkIn
  - NetworkOut
  - NetworkPacketsIn
  - NetworkPacketsOut
* CloudWatch Metrics Client VPN
  - Egress bytes
  - Ingress bytes
  - Egress packets
  - Ingress packets
  - Authentication failures (count)
  - Active connections (count)
* CloudWatch Logs
  - VPC Flow logs
  - EC2-published log streams
  - AWS Client VPN authentication attempts
  - CloudTrail does not log network traffic 
* Performance improvement EC2-VPN
  - EC2 network performance tied to instance type and size.
  - Some EC2 instance types support enhanced networking
  - VPN software processing may limit maximum throughput.
  - Some VPN systems can separate encryption and tunnel functions.
* Where to look if its not working
  - Route tables
  - Security groups
  - NACLs
  - Authentication
  - Customer Gateway Devices
  - OpenVPN client configuration
  - Insufficient permissions
    1. Accessing AWS services or objects requires IAM or resource level permissions.
    2. Accessing services hosted on EC2 require OS-level permissions.
  - AWS networking services do not require IAM permissions in order to be used, only to be managed.

### AWS VPN Cost Optimization
* AWS Client VPN
  - Managed client-based VPN allowing secure access between AWS resources and on-premises networks.
* AWS Site-to-Site VPN
  - An IPSec VPN connection between your remote network and your VPCs.
* Scenario Overview
  - 15 employees, who need access to VPC resources.
  - CISO has required that a VPN connection be put into place for access.
  - Employees can optionally connect from home or from the office.
  - Each employee will use the VPN connection every work day (5 days) of the week.
  - Estimate planning expects each employee to download and upload around 8 GB of data per day (each).
  - Team access is the focus. Individual connection issues are not a primary concern.
* Site-to-Site VPN could offer the best performance and security but you have extternal costs, like Customer Gateway Device.
* AWS Client VPN offers a managed, scalable solution with no infrastructure overhead.
* EC2-hosted VPNs allow for the most control, but remember, they have potential overhead costs in addition to infrastructure costs!
* Remember to include data transfer charges, in addition to overhead costs, when deciding.
