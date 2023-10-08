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

### BGP Prefix Preference - Part 1
* 

### BGP Prefix Preference - Part 2

### BGP Prefix Preference - Part 3

### VPN and IPSec Overview

### Customer Gateways

### AWS Site-to-Site VPN Configuration
