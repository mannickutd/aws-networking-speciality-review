# AWS Direct Connect and Hybrid DNS

### Introduction
* Whats wrong with VPNs?
  - AWS Site-to-Site VPN tunnels are only initiated from on-prem to AWS.
  - VPN traffic uses public infrastructure (quality of the conneciton may come into play).
  - VPNs use "out to internet" data transfer billing rates.
  - VGW is limited to a maximum of 1.25 Gbps for all VPN connections.
* Benefits of Direct Connect
  - DX connections are "always on" in both directions.
  - Traffic uses dedicated infrastructure.
  - Reduced rates for data transfer charges.
  - Includes a flat hourly port charge.
  - 10 Gbps max speed per DX connection.
  - Multiple connections may be created and used simultaneously.
* What is Hybrid DNS?
  - Hybrid DNS enables private DNS resolution across connected cloud and on-prem networks.

### Direct Connect Locations & Hardware
* Dedicated, high-throughput low-latency connection to an AWS Region.
* Accessed via globablly placed DX locations.
* AWS customers may provision their own networking hardware at a DX location or work through a DX Partner.
* Customer traffic is isolated using VLANs.
* Hardward requirements
  - Single-mode fiber.
  - Either 1000BASE-LX (for 1Gbps) or 10GBASE-LR (for 10 Gbps) transceivers.
  - Disable auto-netotiation on all ports used with DX.
  - 802.1Q VLAN encapsulation must be supported across entire connection.
  - Devices must support BGP and BGP MD5 authentication.
  - Bidirectional Forwarding Dection (BFD) is supported but not required.

### DX Connections
* Dedicated DX Connection
  - AWS hardware at DX location connects directly to customer-managed hardware.
  - Supports either 1 Gbps or 10 Gbps connection speeds to AWS Region.
  - Soft limit of 10 dedicated connections per Region, per account.
* Request Dedicated DX Connection
  - Create new connection request using console, cli or api.
  - Supply
    1. Name tag.
    2. DX location.
    3. Sub-location if applicable.
    4. Port speed.
    5. Direct Connect Partner, if applicable.
    6. Optional additional tags.
  - New requests may take up to 72 hours for AWS to process.
  - Only tag keys and values may be modified after the connection request has been submitted.
  - After request granted, youre given a letter of authorization connecting facility assignment.
    1. Authorizes DX location to connect your hardware to a specific port on AWS-owned hardware.
    2. Valid for 90 days.
    3. Contact AWS support if download link is not available more than 72 hours after submitting initial request.
* Establish Cross-Connect at DX location
  - DX location connects customer-managed hardware to AWS-owned hardware.
  - Send LOA-CFA to DX location to initiate process.
* What is a DX hosted connection?
  - AWS hardware at DX location connects directly to AWS DX Partner-managed hardware.
  - Wider range of bandwidth options:
    1. Mbps - 50, 100, 200, 300, 400, 500.
    2. Gbps - 1, 2, 5, 10 (only supported by certain partners).

### Virtual Interfaces (VIFs)
* Why do we need Virtual interfaces?
  - DX connections are layer 2 circuits.
  - Communication with AWS services is layer 3.
  - DX virtual interfaces provide layer 3 connectivity.
  - Private VIFs connect to private AWS networks.
  - Private VIFs can attach to either:
    1. A single VGW.
    2. A single Direct Connect gateway.
  - DX gateways facilitate connection to multiple VPCs in multiple regions.
  - Transit VIFs are used when connecting DX to a transit gateway.
  - A DXGW may connect with either:
    1. Private VIFs and VGWs.
    2. Transit VIFs and TGWs.
    3. But not both!
* VIF types
  - Public VIFs, public VIFs connect to AWS public services, in all public regions, without the internet.
* VIF configuration
  - VIF type.
  - VIF name.
  - DX connection name.
  - VIF owner.
  - Gateway (private and transit VIFs)
  - VLAN ID.
  - BGP ASN.
  - Other BGP settings
  - Jumbo MTU (private and transit VIFs).
  - Tags
* After creation
  - Only tags may be edited.
  - Router configuration file may be downloaded.
  - DX dedicated connections support:
    1. Up to 50 public or private VIFs.
    2. Only 1 transit VIF.
  - DX hosted connections only support a single VIF.
* Hosted VIF
  - Allows a DX connection owned by one AWS account to be used by a different account.
  - Hosted VIFs are created by the owner of the DX connection and offered to the other AWS account.
  - Creator account identifies consumer account as the VIF owner.
  - Can be any VIF type.
  - All VIF properties are defined by creator account.
  - Hosted VIF must be accepted by consumer account before use.
  - Private and Transit VIFs must be connected to an appropriate gateway.
  - Once the hosted VIF is accepted, the router configuration file can be downloaded by the creator account, not by the consumer account.

### Virtual LANs (VLANs)
* Virtual LAN
  - Virtual LANs, 802.1Q VLANs are a L2-readable means of identifying traffic belonging to different L3 networks.
  - Virtual LANs, used to isolate traffic in switched networks.
  - Virtual LANs, often represents a different IP subset.
  - Each VLAN must be assigned its own VLAN ID.
* VLAN IDs
  - VLAN IDs are defined by the customer on each device.
  - Must be unique per device.
  - Should be consistently configured across all devices carrying VLAN traffic.
  - Hardware at both the DX location and on-prem must be correctly configured to recognise desired VLANs.
* VLAN Ports
  - Many switches begin with a default VLAN that all ports belong to.
  - Ports are added to one or more admin-defined VLANs.
  - VLANs are assigned a numeric ID at creation.
  - Ports belonging to a single VLAN are referred to as untagged or access ports.
  - Traffic entering an untagged port may only be sent to another port that is a member of the same VLAN.
  - Misconfigured ports will prevent communication.
  - In larger switched environments, VLANs may need to extend across multiple devices.
  - VLAN IDs should remain consistent across all switches.
  - While directly connecting untagged ports in each VLAN will allow communication. More commonly is to make a single port a member of multiple VLANs.
  - Ports belonging to more than one VLAN are referred to as tagged or trunk ports.
* Tagged vs Untagged ports and traffic
  - When traffic leaves an untagged port:
    1. Standard Ethernet frame is used.
    2. VLAN is known because of the ingress port configuration.
  - When traffic leaves a tagged port:
    1. An 802.1Q Ethernet frame is used.
    2. VLAN ID is contained within the 802.1Q "tag".
  - Mismatched tagged traffic or untagged traffic received at a tagged port could either be dropped or sent to the switchs default VLAN.
* Only One VIF with a Hosted DX Connection?
  - How do you support multiple VLANs when using hosted DX connections?
    1. Establish multiple hosted DX connections.
    2. Use aggregated VLANs.
* Aggregated/Nested/"Q-in-Q" VLANs
  - On-prem and DX location devices are configured to recognize a specific "type" or VLAN tag.
  - Tagged traffic with the configured tag type is handled normally.
  - Tagged traffic of any other type is treated as untagged traffic.
  - Have the DX location coordinate with AWS to remove the provider tag from your traffic.
  - VLAN traffic may only be sent to the gateway your single VIF is attached to.

### Virtual Interfaces & BGP
* VIF BGP Requirements
  - All VIFs must be connected to an on-prem BGP peer.
  - Both IPv4 and IPv6 are supported.
  - BGP MD5 authentication is required.
  - Some DX locations support two peers for one VIF.
* BGP Prefix Advertisements
  - Private VIF 100 is the max number of prefixes.
  - Public VIF 1000 is the max number of prefixes
  - Exceeding this limit will cause the BGP sessions to go to the IDLE state.
  - VGWs associated with private VIFs advertise all known routes.
  - VGWs associated with DX gateways must specify allowed prefixes to be advertised.
  - For public VIFs, AWS advertises prefixes for
    1. All public services in all public AWS regions.
    2. Non-region services such as CloudFront and Route53.
  - Control outbound traffic to these prefixes at your on-prem router:
    1. Filter outbound traffic with ACLs or firewalls.
    2. Filter learned prefixes using BGP communities.
* BGP Communities
  - A means of labelling BGP prefixes.
  - BGP routers can be configured to handle incoming or outgoing prefixes based on their community values.
  - Comprised of 16-bit ASN and a 16-bit, organization-defined number.
* AWS Prefix BGP Communities
  - AWS automatically applies the following communities to prefixes advertised to public VIFs:
    1. 7224:8100 - routes to services from the same region as the DX connection.
    2. 7224:8200 - routes to services from the same continent as the DX connection.
    3. No value - routes to global services
  - AWS advertised prefixes also include the "no_export" BGP community.
* Local customer prefixes and BGP Communities.
  - Customer prefix advertisement is controlled at public VIF.
  - Use BGP communities to control where AWS can propagate customer prefixes:
    1. Local AWS region - 7224:9100
    2. All regions in a continent - 7224:9200
    3. All public regions - 7224:9300

### Link Aggregation Groups (LAGs)
* What is?
  - A collection of multiple physical links combined into a single, logical link.
  - Traffic sent to the LAG is distributed across all member links.
  - Aggregates throughput of member links.
  - Provides resiliency in the event of member link failure.
* LAG requirements in Direct Connect
  - All DX connections in a LAG must:
    1. Use the same bandwidth.
    2. Terminate at the same DX location.
  - Maximum of four connections per LAG.
  - Maximum of 10 LAGs per region.
* Lag Creation
  - Use existing connections, request new connections, or a mix of both.
  - You cannot create a LAG with new connections if you would exceed the overall connection limit for the region.
  - Adding existing connections to a LAG will temporarily interrupt connectivity.
  - Minimum links identifies the minimum number of functional connections necessary for the entire link to be functional
    1. If the nubmer of active links drops below the minimum, the entire LAG connection will become non-operational.
    2. Default value is 0 (no minimum).
* LAGs and VIFs
  - VIFs may be attached to a LAG instead of a single DX connection.
  - A corresponding customer LAG must be created at the on-prem hardware.
  - Add LAG primary port to VIF VLAN.

### Direct Connect Gateways
* What are Direct Connect Gateways?
  - Global services that facilitate DX connectivity to multiple AWS regions.
  - A bridge between Private or Transit VIFs and AWS networking objects.
  - No interactions with Public VIFs.
  - Are free of charge.
* Direct Connect Gateway and Private VIFs
  - By itself a private VIF can only connect to a single VGW.
  - Connected VGWs must be in the same region used by the DX connection.
  - DX gateway may connect a single private VIF with up to 10 VGWs in any public region.
  - Up to 30 private VIFs may connect to the same DX gateway.
* DX Gateway IP Traffic limitations
  - VPCs connecting through a DX gateway cannot have overlapping IP ranges.
  - Only sessions via a single VIF to one connected VPC at a time are allowed.
  - You cannot send traffic:
    1. From one associated VPC to another.
    2. From one connected VIF to another.
    3. From a connected VIF through a VPN connection using an associated VPG.
* Direct Connect Gateways and Transit VIFs
  - DX gateways are also used when attaching DX connections to Transit gateways.
  - A DXGW may connect with either:
    1. Private VIFs and VGWs.
    2. Transit VIFs adn TGWs.
* Other DX Gateway odd-bits
  - VGWs in one AWS account may request associated with a DX gateway in a different account.
  - DX gateways may only be associated with VGWs attached to a VPC.
  - Each VIF and VGW may only be associated with a single DX gateway.
  - A VGW can be simultaneously attached to both a single private VIF and a single DX gateway (connected to a different private VIF).
* Configure DX Gateway
  - Name.
  - Amazon side ASN.
  - Attach to VIF.
  - Associate with VGW or TGW.

### Direct Connection Security MACsec
* What is?
  - IEEE Layer 2 standard providing data confidentiality, integrity, and origin authenticity.
  - Important, Direct Connect is NOT an encrypted traffic channel.
  - Must configure Direct Connect connections for MACsec (limited partners).
  - Adds 8-byte header and 16-byte tail to all ethernet frames.
  - May still need IPSec VPNs in addition to MACsec.
* Key concepts and components
  - Create a CKN/CAK* pair for use as MACsec secret key.
  - MACsec key agreement decides how the connection leverages a pre-shared key.
  - Associate the CKN/CAK pair with both the DX interface or LAG as well as the customer router.
  - Configuration options include 'should_encrypt', 'must_encrypt' and 'no_encrypt'.

### Well-Architected Direct Connect
* Resiliency - Multiple Connections
  - Implement multiple DX connections to increase resiliency.
    1. Multiple connections at single DX location.
    2. Multiple DX locations.
  - Implement LAGs.
  - Direct Connect Resiliency Toolkit.
* Resiliency - Bidirectional Forwarding Detection
  - Routing protocols do not quickly recognize link failures.
    1. BGP default peer timeout is 270 seconds.
  - Bidirectional forwarding detection can detect link failures and respond within milliseconds.
    1. Informs associated routing protocol to recalculate best paths.
  - Automatically enabled on VIFs.
  - Must be configured at customer devices.
    1. Tx/Rx intervals in milliseconds.
    2. Interval multiplier.
* Resiliency - Failover to VPN
  - BGP prefix preference configuration can be used to initiate VPN connections if DX paths are down.
  - AWS VPN creation can be automated.
  - Create VPN server AMIs.
* Resiliency and Performance - Local BGP communities
  - Control prefix-preference for AWS traffic returning to your network.
  - Used with multiple DX connections to configure load balancing or failover.
  - Supported over Private and Transit VIFs.
  - Community values:
    1. Low preference - 7224:7100
    2. Medium preference - 7224:7200
    3. High preference - 7224:7300
  - Traffic will be sent to higher preference prefixes over lower prefixes.
  - Traffic is load balanced across multiple, equal-preference prefixes.
* Performance - LAGs
  - LAGs can be implemented to aggregate throughput from multiple connections.
  - 4x10 Gbps physical connections = 1x40 Gbps logical connection.
* Performance - Jumbo Frames
  - Enabling jumbo frames for private VIFs sets MTU from 1500 to 9001 bytes.
    1. More data sent per Ethernet packet.
    2. Total data transmitted with fewer packets.
  - Enabling/disabling jumbo frames on a VIF disrupts traffic for all VIFs on the connection for up to 30 seconds.
  - Ensure that support for jumbo frames is enabled from end to end.
  - Using nested VLANs might cause occasional data corruption if jumbo frames are not enabled.
  - Not supported via VPG static routes.
* Security - VPN over DX
  - AWS does not encrypt DX traffic.
  - VPN tunnels may be established from on-prem to EC2-based VPN systems.
    1. AWS Site-to-Site VPN via Public VIF.
    2. Private VIF - EC2 hosted VPN.
* Cost Optimization - DX DTO vs Internet DTO
  - Direct Connect has two billing elements
    1. Port Hours
    2. Data Transfer Out (DTO)
  - DTO rates vary depending on the source AWS region and the DX location the traffic is sent to.
  - DX DTO rate is not reduced by increased usage.
  - Data transferred in is always free.
* Cost Optimization - DX DTO vs Internet DTO Scenario
  - For "x" amount of DTO, when does DX become cheaper than internet DTO?
    1. Scenario conditions:
       a. Internet DTO rate 0.05/GB
       b. DX DTO rate: 0.02/GB
       c. 1G Port 0.30/hour
       d. 10G Port 2.25/hour
    2. (.05x) - ((.02x) + (port rate * time)) NOTE: Negative amounts mean that DX is more expensive.
* Operational Excellence - Monitoring
  - CloudWatch metrics for DX connections:
    1. ConnectionState.
    2. ConnectionCRCErrorCount.
    3. ConnectionBpsEgress/Ingress.
    4. ConnectionLightLevelTx/Rx.
    5. ConnectionPpsEgress/Ingress.
  - VIFs do not have CW metrics.
    1. VIF status must be checked manually.
* Operational Excellence - Troubleshooting
  - No connectivity to AWS device specified in LOA-CFA?
    1. Physical layer problem at on-prem or DX location.
    2. Check cable and port configuration.
    3. Ensure DX hardware requirements are followed.
  - DX connection is up but VIFs stay down.
    1. Data link layer problem.
    2. Verify 802.1Q support.
    3. Ensure correct VLAN and VIF configuration.
  - Connection up but can't establish BGP session?
    1. Misconfigured:
       a. ASNs.
       b. Peer IP address.
       c. MD5 authentication key.
       d. Exceeded max number of advertised prefixes.
       e. BGP port (TCP 179) is blocked.
  - Connection, VIF, and BGP session are up but you can't reach AWS resources.
    1. Routing to AWS resources correctly configured?
    2. NACLs and security groups correctly configured?
    3. Software at AWS resources correctly configured?   

### Hybrid DNS
* What is Hybrid DNS?
  - Private DNS resolution across hybrid networks.
    1. AWS resources are able to use on-prem DNS zones.
    2. On-prem resources are able to use Route 53 private zones or VPC DNS.
* Route 53 Resolver
  - Provides default DNS resolution within VPCs.
  - "VPC +2" IP address.
  - Part of EC2 service hardware.
  - Route 53 Private Zones must be associated to VPCs.
  - Resolver search sequence:
    1. Route 53 Private Zones.
    2. VPC DNS domain.
    3. Public DNS system.
* They Hybrid DNS Challenge
  - EC2 instances always use Route 53 Resolver by default.
  - On-prem systems cannot reach Route 53 Resolver.
* Customer implemented DNS Resolvers
  - EC2-hosted DNS resolvers are provisioned within VPC.
  - VPC configured to use EC2 resolver instead of Route 53.
  - Resolver forwards matching requests to on-prem DNS.
  - On-prem DNS resolvers configured to forward matching request to EC2 resolver.
* AWS Directory Servers as DNS Resolvers
  - AWS Directory Services Simple AD can provide the same functionality.
  - Configure on-prem DNS to forward to Simple AD DNS addresses.
* Customer-implemented DNS Resolvers - Problems
  - Single EC2-ENI limited to 1024 queries/second.
  - Multi-AZ deployment needed for HA.
  - Most DNS clients don't load-balance across multiple servers.
  - DNS resolver services can often load balance.
* Route 53 Resolver Endpoints
  - Provides IP accessible endpoints to the AWS Route 53 Resolver service.
  - Endpoints support from 2 to 8 ENIs.
  - Each ENI supports up to 10000 queries/second.
  - Endpoints are created within a single VPC, but maybe used by other VPCs in the same region.
  - Secured by a single VPC security group.
    1. Group assignment cannot be changed after creation.
  - Each endpoint can handle either inbound or outbound DNS requests.
  - Endpoint ENIs are AZ-scoped resources.
    1. Place your endpoints in separate AZs for availability.
  - ENIs use either dynamic or customer-assigned IP addresses.
  - IP addresses are persistent for the lifetime of the endpoint.
  - Pricing per ENI, per hour.
* Inbound Endpoints
  - Handles requet forwarding from on-prem to AWS DNS resolver.
  - Requests are sent to the IP address of an ENI.
  - Request from on-prem DNS resolver instead of client for better performance.
  - Private hosted zones must be associated to the VPC where resolver endpoints reside.
* Outbound Endpoints
  - Handle forwarding for requests originating within AWS.
  - Can be associated with multiple VPCs in a region.
* Outbound Endpoint Traffic Rules
  - Specify forwarding action for requests matching defined FQDN patterns.
  - Forward rules forward requests to IPv4 address of on-prem DNS.
  - System rules forward requests to Route 53 resolver.
  - System rules are automatically created for:
    1. Private hosted zones.
    2. VPC domain names.
    3. Publicly reserved domain names.
  - If rules conflict, resolver prefers:
    1. Most specific FQDN
    2. Forward rules over system rules.

### DNS Using Route 53 Resolver Endpoints (Inbound and Outbound)
* Inbound Resolver Endpoints
  - Traffic inbound to VPCs
    1. DNS resolvers send traffic to these endpoints to leverage to Route 53 Resolver in VPCs.
  - Resolve AWS Domain Names
    1. Enables on-premises DNS resolvers to resolve AWS resources and records that live within VPCs and private hosted zones.
* Outbound Resolver Endpoints
  - Traffic outbound from VPCs
    1. Conditionally forwards DNS queries to DNS resolvers on-premises.
  - Leverages Resolver Rules
    1. Resolver rules determine which DNS query traffic to forward to your DNS resolvers outside of AWS.
* Resolver Endpoint Concepts to Know
  - You associate BOTH endpoint types with one or more VPCs within a single AWS Region.
  - For an HA implementation, you create each endpoint type in two different AZs.
  - High-throughput! Resolver endpoints support up to 10000 queries per second per IP address.
* Lets Talk Resolver Rules
  - Dictate which DNS queries get forwarded to no-premises DNS resolvers.
  - Condition forwarding rules: Forwards DNS queries for a specific domain name to DNS resolver on your internal network. Also known as forwarding rules.
  - System rules: Allow for selective overrides for behaviours defined in your forwarding rules.
  - Recursive rules: Internet resolver rule. Automatically created for resolving any domain names not specified in your custom rules.
  - Auto-defined rules: Rules that are automatically created for selected domains. Usually AWS-specific domains and private hosted zones.
* Import Resolver Rule Exam Concepts
  - If there are multiple rules that match a DNS query, the MOST specific one is chosen.
  - You can leverage AWS RAM to centrally manage and share Resolver rules across an organization.
  - Sharing allows cross-account DNS resolver rules usage from multiple VPCs. Centralized egress and ingress.
