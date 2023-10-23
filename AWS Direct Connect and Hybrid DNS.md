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

### Hybrid DNS

### DNS Using Route 53 Resolver Endpoints (Inbound and Outbound)


