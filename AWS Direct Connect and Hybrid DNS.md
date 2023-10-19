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
* 

### Link Aggregation Groups (LAGs)

### Direct Connect Gateways

### Direct Connection Security MACsec

### Well-Architected Direct Connect

### Hybrid DNS

### DNS Using Route 53 Resolver Endpoints (Inbound and Outbound)


