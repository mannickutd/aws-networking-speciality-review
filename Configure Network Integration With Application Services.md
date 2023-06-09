# Configure Network Integration with Application Services

## Domain Name System

A globally distributed service that translates domain names to IP addresses.
Top level domain is the last part of the domain eg. .com, .au, .org, .net
Second level domain is the preceeding part before the top level domain eg. .amazon, .google, .microsoft.
Third level domain is the preceeding subdomain part before the second level domain eg. .aws, .mail, 
Fully Qualified Domain Name (FQDN) - is made up of aws.amazon.com.

## Name Servers

Name servers respond to queries and do most of the work of DNS. Name servers can be authoritative and non-authoritative.
* Authoritative - Provides answers to queries they know about.
* Non-Authoritative - Points to other servers or serves cached content from other name servers data.

## Route 53
Is an authoritative name server.
* Provides direct updates to manage public DNS names.
* Answers DNS queries, translates domain names into IP addresses.

## Zones

A zone is a container that holds important information about how to route traffic for domain names and their subdomains.

## DNS Resolution

* Checks locally, the client computer will try to resolve the address locally. It looks at the hosts file configured locally and stored in a cache.
* Forwards request, if nothing is found it will forward the request to the resolving name server. This is usually the ISP, the router of the network or configured in your LAN.
* Waits for a response, resolving name servers take care of all requesting queries which are transparent to the client. Once an IP is found it is returned to the client.

## Record Types

| Record | Record Types |
| ------ | ------------ |
| A and AAAA | Maps a host to an IP address. |
| CNAME | Canonical Name records defines an alias for a host. |
| NS | Name Servers records direct traffic to the DNS servers that contain the authoritative DNS records. |
| MX | Mail Exchange records are used to define the mail servers. |
| TXT | Text records hold text information. |
| PTR | Pointer record is a reverse A record lookup. Maps an IP address to a host. |
| SOA | Start of authority. |
| CAA | Certificate authority authorization. |
| NAPTR | Name authority pointer. |
| SPF | Send policy framework. |
| SRV | Service locator. |

## Route 53 Hosted Zones and Zone Records

Route 53:
* Managed DNS service.
* Uses AWS Global infrastructure.
* Includes features unique to AWS.

Hosted zones can be public and private:
* Public zones are for DNS request over the public internet.
* Private zones are for internal DNS traffic from selected VPCs.

Creating a public hosted zone assumes correctly configured:
* Public domain name registration.
* Delegation of domain authority.

** NOTE: ** Registering a domain name through route53 automatically creates a public hosted zone.

Private Hosted Zones
* Must be associated with one or more VPCs. VPCs must have DNS support and DNS hostnames enabled.
* Private domain names do not need to be registered with ICANN.

CNAME vs Alias
| CNAME | Alias |
| ----- | ----- |
| DNS record type. | Not a DNS record type. |
| Referred to as an alias. | AWS Route 53 extension. |
| Redirects to other DNS record names | Redirects to AWS service object FQDNs |
| Returns FQDN to client | Returns IP address to client. |
| Charge per query. | No query charge. |

## Subdomain Delegation

* DNS subdomain delegation allows separation of zone management.
* Parent zones require an NS record for the subdomains authoritative DNS servers.
* Public zones may not delegate subdomains to private zones.
* Private zones may not delegate their own subdomains.

## Routing Policies

Routing policies determine how to respond to queries. We can set a routing policy whenever we create a record set in Route53.

| Routing Policies | Purpose |
| ---------------- | ------- |
| Simple | Plain and simple routing. |
| Weighted | Percentage routing. |
| Latency based | Fastest connection routing. |
| Failover | One or the other routing. |
| Geolocation | Location based routing. |

Weighted policy - the probability of any resource record set being called is determined by (weight of a specific resource)/(sum of weights of all records). The sum of all weights can change if resources fail the healthcheck and are removed from the set.

Failover routing policy consists of two record sets, A (primary) and B (secondary). 
* If set A is healthy, record set A will always be returned.
* If set A is unhealthy, record set B will be returned.
* If set A and B are unhealthy, record set A will be returned.

Latency routing policy - When a DNS query is received by Route 53 for your domain or sub-domain, it evaluates the latency records that you have created for AWS Regions. Then, it will determine which region provides the lowest latency to the user, and then selects the appropriate latency record for that region.

Geolocation routing policy:
* Geolocation routing lets you choose the resources that serve your traffic based on the geographic location of your users, meaning the location that DNS queries originate from. For example, you might want all queries from Europe to be routed to an ELB load balancer in the Frankfurt region.
* You can specify geographic locations by continent, by country, or by state in the United States. If you create separate records for overlapping geographic regions—for example, one record for North America and one for Canada—priority goes to the smallest geographic region. This allows you to route some queries for a continent to one resource and to route queries for selected countries on that continent to a different resource.
* Geolocation works by mapping IP addresses to locations. However, some IP addresses aren't mapped to geographic locations, so even if you create geolocation records that cover all seven continents, Amazon Route 53 will receive some DNS queries from locations that it can't identify. You can create a default record that handles both queries from IP addresses that aren't mapped to any location and queries that come from locations that you haven't created geolocation records for. If you don't create a default record, Route 53 returns a "no answer" response for queries from those locations.

Multivalue response routing policy
* Multiple record sets (up to 8) can be returned with the query.
* Only healthy host records are returned in the query.

## Route 53 Health Checks

* Monitor resource health.
* Provide notification of status change.
* Used for DNS failover.

Endpoint health checks:
* Tests specific endpoints for responsiveness.
* Define target endpoints by:
  - IP address or domain name.
  - Protocol used for the check (tcp, http, https).
  - Port number.
  - Optional file paths for http/https checks.

** Note: ** - Domain name health check gotchas.
* Domain name health checks only use IPv4 addresses.
* Healthy domain name health checks to domain names with multiple values will return IP addresses for failed nodes.

Summary:
* Route 53 health checks prevent requests being sent to offline endpoints.
* DNS failover requires configuring multiple records or IP addresses for a hostname.
* Use IP-address endpoint health checks for resources hosted on redundant endpoints.
* Creating health checks for alias records is unnecessary.

## Private Hosted Zones

A private hosted zone only responds to queries coming from within the associated VPC and it is not used for hosting a website that need to be publicly accessed. The main use case of a private hosted zone is split-view DNS. We can use Amazon Route 53 to configure split-view DNS, also known as split-horizon DNS.

## Elastic Load Balancer

* Single logical target for service requests.
* Requests distributed across backend servers.

### AWS Elastic Load Balancer
* Highly available.
* Regional resource.
* Auto-scales for workload.
* Protected by security groups.
* Can terminate https/tls connections.

### ELB Types:
* Application load balancer (ALB)
 - Layer 7.
 - HTTP and HTTPS.
 - Routes requests using http header content.
* Network Load Balancer
 - Layer 4.
 - TCP, UDP, TLS.
 - Route requests by protocol and port number.
* Classic Load Balancer (ELB)
 - Layer 4 and 7.
 - HTTP, HTTPS, TLS, TCP.
 - Route requests by protocol and port number.
 - Legacy option.

### Gateway Load Balancer
* Works with applications that operate in-line with network traffic.
 - Security
 - Analytics
* Uses GENEVE protocol to encapsulate traffic.
 - Allows original IP traffic to be unchanged.
* Handles traffic to and from service targets.
 - Handles layers 3 and 4.

| Type | Pros and Cons |
| ---- | ---- |
| ELB | Supports ec2-classic, legacy option, ALB and NLB offer more flexibility. |
| ALB | "Web" application and services. Customizable request handling based on HTTP header count. |
| NLB | Forwards only by protocol and port. Better performance and scalability than ALB. Component of endpoint services and vpc traffic mirroring. |

### Core ELB Concepts
* Targets
 - Backend resources that requests are forwarded to.
 - Can be in different AZs.
 - Health evaluated before use.
 - Types vary by ELB type.
* Target Groups
 - Logical container for targets.
 - Scope configuration of many settings (health checks, expected traffic).
* Listeners
 - Processes that check for connection requests to specific protocols and ports.
 - Matching traffic forwarded to targets.
 - Settings and options vary by ELB type.

### ELB Connectivity
* Each ELB works within a single VPC.
* Requests are sent to an AWS assigned FQDN.
* Public if ELB is internet facing or private for internal use.

### AZ presence
* ELB must be configured to work within at least one AZ. Use two AZ to support high availability.
* Worker nodes created in each configured subnet. Additional nodes added when scaling.
* Each node accessed by IP address.

### Request Routing
* Requests to ELB will be evenly distributed across all nodes (round-robin).
* Nodes determine which targets to receieve requests.
* If cross zone enabled, ELB will route requests across AZ to other targets.



