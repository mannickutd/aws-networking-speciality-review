# Transitive Networking

### Overview
* Transitive networking occurs when traffic in-between two network nodes passes through one or more additional nodes.
* "Nodes" can be individual network hosts (such as a router or switch)
* Nodes can be entire networks as well.
* Configuring redundant connections can be complicated and expensive.
* AWS managed networking services are intrinsically highly available
* Traffic can be collectively managed at transit nodes.
* Network packets originating outside of a VPC must have a NIC within that VPC as a destination or they will be dropped.
* VPCs are not transitive via VPC peering connections
* VGWs cannot forward traffic outside of its attached VPC.
* Services using interface VPC endpoints can be accessed.
* Traffic originating outside of a VPC cannot access services via a gateway endpoint.

### Inter-VPC Connectivity
* VPC sharing.
  1. VPC sharing allows a subnet in one AWS acccount to be shared with other AWS accounts.
  2. Accounts must be part of the same AWS Organization.
  3. Subnets are shared via AWS Resource Access Manager.
  4. Once shared, other accounts may provision resources within that subnet as if it were native to their account.
  5. Only the owner account has management control over that subnet.
* VPC peering
  1. VPC peering connections do not allow transitive networking.
  2. VPC peering is not restricted by AWS account or region.
  3. Data transfer charges apply to inter-VPC traffic.
  4. Minimum required configuration allows broad inter-VPC connectivity.
* VPC endpoint services
  1. Exposes an NLB-frontend application to selected consumers in the same region.
  2. Consumers connect to application by creating a VPC interfae endpoint.
  3. Access can be restricted by AWS account, IAM user, or IAM role.
* Transit VPCs
  1. Transit VPCs contain an EC2-hosted VPN solution.
  2. EC2 VPN solution is the customer gateway for AWS VPN connections to spoke VPCs.
  3. VPN connections over the internet allow VPCs to be in any AWS account in any region.
  4. Additional EC2 VPN systems should be implemented for high availability.
  5. Dynamic routing is strongly recommend but is not a requirement.
  6. Spoke VPC VGWs advertise their local CIDR prefix to transit VPC VPN systems.
  7. VPN systems advertise all prefixes back to spoke VPCs.
  8. Spoke VPC VGWs route traffic to other spoke VPCs via the transit VPCs VPN systems
* Patterns-Trusted VPCs
  1. VPCs that trust one another can still be connected by VPC peering
  2. Static routes using PCX will still need to be manually added to the VPC route tables.
  3. Resources or services used by other VPCs may be placed in a central VPC.
  4. Service VPCs are connected to transit VPCs in the same way as other spoke VPCs.
  5. Access to service VPCs may require connecting through authentication or security services in the transit VPC.
  6. Trusted VPCs may be directly peered.
  7. Spoke VPC uses EC2-hosted VPN instead of VGW and AWS VPN connection.
  8. Can be used to overcome AWS VPN connection limitations.
  9. Customer is responsible for maintaining unmanaged services.

### Transit VPCs and Hybrid Connectivity
* VPNs from on-prem networks should connect to the VPN system in the transit VPC.
* Optionally, AWS VPN connections may be established with trusted spoke VPCs.
* AWS VPN connections to a VGW in the transit VPC cannot forward traffic to other VPCs.
* Direct Connect
  1. Public VIFs
     a. Allows access to AWS public services without traversing the internet.
  2. Private VIFs
     a. Can connect to a single VGW to access attached VPC.
     b. Can connect to a Direct Connect Gateway.
     c. up to 10 VGWs in any public region may connect to a DX Gateway.
  3. If Direct Connect traffic must pass through the transit VPC, then a detached VGW must be created in the region the DX location connects to.
  4. Private VIF is associated with detached VGW.
  5. The VPN systems in the transit VPC connect with AWS VPN connections.
  6. If spoke VPCs must be accessed directly, new private VIFs could be connected to their VGWs directly.
  7. Direct Connect Gateway can be used to connect to multiple spoke VPCs.
  8. DX gateway cannot associate with floating VGW.
* Jumbo frame roundup
  1. Traffic with a larger MTU than the network can support will be fragmented.
  2. Enabling "Do Not Fragment" IP header flag will cause large traffic to be dropped instead.
  3. Default size 1500 bytes
  4. Max supported MTU sizes
     a. VPC: 9001
     b. DX private VIF: 9001
     c. DX transit VIF: 8500
     d. DX public VIF: 1500
     e. VPC peering: 1500
     f. Internet: 1500

  ### AWS Transit Gateway Configuration
  * What is a Transit Gateway TGW?
    - Networking resource that allows interconnection of thousands of VPCs and on-premises networks.
    - Supports multiple routing domains and ECMP.
    - Soft limit of five TGWs per account.
    - Attachments include:
      1. Single/multiple VPCs
      2. VPN
      3. DX Gateways
      4. TGW peering connections
      5. Third party networking appliances.
  * Configuration
    - DNS support, enable Domain Name System resolution for VPCs attached to this transit gateway.
    - VPN ECMP support, equal cost multi-path (ECMP) routing for VPN connections that are attached to this transit gateway.
    - Default RT association, automatically associate transit gateway attachments with this transit gateway's default route table.
    - Default RT propagation, automatically propagate transit gateway attachments with this transit gateways default route table.
    - Multcast support, enables the ability to create multicast domains in this transit gateway. 
  * Concepts
    - TGWs are connected to AWS network objects via attachments.
    - TGW attachments are AWS objcts with their own AWS-assigned identifier.
    - TGWs can have attachments to VPCs, VPN Customer Gateways, DX Gateways, peered TGWs and Third-Party SD-WAN/Network Appliances.
    - Traffic from an attached network arrives at the TGW from that network's attachment.
    - Customers are billed hourly per operational TGW attachment.
  * Attachments
    - Attachments may be configured to automatically propagate known CIDR ranges into one or more TGW route tables.
    - Attachments do not need to be associated with a route table in order to propagate to it.
    - VPCs will propagate their local CIDR.
    - VPN CGWs and DGWs will propagate customer prefixes advertised via BGP.
    - Disabling TGW route propagation will require static route configuration.
  * Static Routes
    - Static routes may be added to point select traffic toward a specific attachment.
    - Only one "default" route per route table.
    - Blackhole routes drop traffic matching the CIDR.
    - Required to forward traffic to a peered TGW.
  
