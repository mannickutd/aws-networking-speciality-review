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

### ELB Target Groups
* Define a target protocol and port of expected traffic, health check settings and elb routing properties.

* Target groups identify back-end targets and how they will be accessed.
* Target groups define health check settings used by the load balancers.
* Target groups session stickiness ensures related requests are handled by the same target.
* Classic load balancers configure target settings locally instead of using target groups.

* ALB - application load balancer (http, https).
* NLB - network load balancer (TCP, UDP, TLS, TCP_UDP).
* GWLB - gateway load balancer (GENEVE).

* ELBs send health check messages to all registered targets. A target must respond in order to be forwarded requests from ELB. 

* HTTP/HTTPS health check settings include, interval, port, success codes and timeout, healthy threshold and unhealthy threshold.
* TCP health check settings include, threshold, timeout is 10s, interval is either 10s or 30s.

* Target registration/deregistration can occur at any time and when connected to autoscaling automates this process.
* Targets may be members of multiple target groups.
* Newly registrated targets must reach healty threshold before receiving requests.

* Session stickiness allows requests to be consistently forwarded to the same target. In order to take advantage of session stickiness the requesting client needs to support cookies.
* Session cookies are encrypted.

### Application Load Balancers
* Layer 7 load balancer.
* Routes traffic to targets based on HTTP/HTTPS header content.
* Support for HTTP 1.1 and 2
* Websockets
* Request-authentication using Cognito or OIDC compliant provider.

* ALB settings groups:
  - General, name, scheme, ip address type. Listeners identify request traffic of interest.
  - Availability zones, vpc, at least 2 AZs, one subnet per AZ. Subnets /27 bitmask or larger, 8 free IP addresses, internet facing scheme must use public subnets.
  - Security settings include, default certificate for HTTPS listeners. Additional certificates may be added later and optional step if no HTTPS listeners.
  - You will need to attach a target group, either a new target group or an existing target group.

* X-Forwarded headers, non standard http headers. ALB supported headers include X-Forwarded-For, X-Forwarded-Proto, X-Forwarded-Port.

** ALB Listener rules
* Rules allow granular routing of traffic matching a listener.
* Listeners always start with a 'default' rules (cannot be deleted).
* Each rule specifies, request header conditions to match, action to perform for matching requests and priority order.
* Rule considitions include, host header, path, http header, http methods, query string pairs or just values and source IP IPv4 or IPv6.
* Multiple conditions may be used.
* No more than one of host header, path, http request method or source IP.
* Comparison limits 3 per-condition, 5 per rule.

### Network Load Balancers
* Layer 4 load balancer.
* Routes traffic to targets based on protocol and port number.
* Scalable to millions of requests per second.
* May be assigned static IP addresses.

* Supported listener protocols and target group protocols (TCP, UDP, TLS and TCP_UDP)
* Listeners always forward matching traffic to selected target group.
* Internet facing IPv4 is a elastic IP address, IPv6 is manually assigned.
* Internet scheme uses manually-assigned private address from associated subnet.

* TCP/UDP/TLS Target Group Attributes
  - Deregistration delay.
  - Connection termination on deregistration.
  - Session stickiness is supported only by source IP address, not supported on TLS listeners.

* NLB private IP always used as source IP for private link and IPv6 converted to IPv4.

* Endpoint services, service access to other AWS principals. Accessed using private DNS name and interface endpoints.
* VPC traffic mirroring, monitoring services in target group, NLB selected as traffic mirror target.

### Classic Load Balancers
* Layer 4 and layer 7 http/https, TCP and SSL/TLS.
* Routes traffic to targets based on protocol and port number.
* Supports EC2-Classic, legacy option.

* Difference to the ALB, Target groups are not used, so all routing in configured at CLB.
  - Target protocol.
  - Health checks.
  - Target registration.
  - Attributes, cross-zone load balancing, connection draining, sticky sessions, proxy protocol.
 
* Instances launched into shared customer network.
* Not available for AWS accounts created after December 2013.

### VPC Endpoint Services
- Allow AWS accounts to offer application services to other AWS principals.
* Provider provisions service instances within ELB target group, must be associated with NLB or GWLB.
* Consumers request service access by creating interface endpoints.
* May be in different AWS accounts.
* Must be in the same AWS region as provider.
* May be revoked at any time.
* Consumer endpoints may be accessed by AWS hybrid networks.
* Provider application provisioned as NLB/GWLB target group member. Provider is responsible for application.
* Endpoint service creation, select load balancer, manual or automatic request acceptance, or private dns name.

### Gateway Load Balancers
* Layer 3 gateway, accessed as an endpoint service.
* You cannot directly route traffic to a GWLB, it is not assigned a FQDN.
* After traffic is routed to it, it behaves very similar to a layer 4 load balancer.
* All traffic forwarded to target group.
* Encapsulated using Geneve protocol (Generic Network Virtualization Encapsulation).
* Original IP data of the packet is not changed.
* Provides ELB benefits to security, management and analytics services.
* Pre-GWLB implementation was complicated to provision and manage.
* No IPv6 support.
* Does not encrypt/decrypt.
* Single listener accepts all traffic.
* All traffic forwarded to selected target group.
* Must use GENEVE Protocol.
* Instance and IP address targets only.
* Targets must support GENEVE protocol, GENEVE TLVs, allow traffic from GWLB.

### Load balancing with Amazon ECS
- ECS Services can be easily integrated with Elastic Load Balancers for even distribution of traffic.
* Supported ELBS
  - ALB
  - NLB

* Considerations for ELBs
  - You can only attach five target groups to a single service.
  - Multiple target groups in a service must use the rolling update deployment controller type.
  - You can combine internal and external load balancers for traffic.
  - Using the awsvpc networking type requires the use of 'ip' target type (specific to fargate services).
  - Containers can belong to multiple target groups to allow for flexibility.

* Concepts to Know
  - ELB support the use of dynamic host port mappings.
  - Dynamic host mapping example: Container Port 80, Host Port 0.
  - ALBs support path-based routing and priority rules.
  - Using IP target type for NLBs means requests are seen as coming from the NLB private IP address.
  - That means tasks are essentially open to the world once requests are allowed.

* Multiple Target Groups
  - Multiple targets groups could be used to support multiple paths of the same service supported by different containers, perhaps they have different latency requirements.
 
### Hosting CDNs with Amaxon Cloudfront
- Amazon cloudfront operates at layer 7.

* Important terms and concepts
  - Origin server: Location of stored content for distributions to use.
  - Distribution: Configuration telling cloudfront which origin server to use.
  - Domain name: Cloudfront assigned DNS for use in web requests.
  - Edge location: Geographically-dispersed servers that cache your files.
  - Edges: Points of presence. S3 or custom origins.

* Amazon cloudfront architecture
  - Missed cache:
    1. Client requests an artifact.
    2. The closest edge location is checked, if missing
    3. The regional edge location is checked if missing
    4. the request is forwarded to the origin.
  - Cache Behavior
    1. Client requests an artifact.
    2. The load balancer checks if the path has "*.png" in the name if yes
    3. Use the cache behavior specific for the rule for "*.png".
    4. Fetch from separate origin server.

* Amazon cloudfront cache behaviors
  - Default cache behavior, required for all distributions.
  - Additional cache behaviors to define responses based on requests. Eg. match for specific path pattern, like *.png and send to S3 origin.
  - Cache behaviors use one specific origin based on settings.
  - If you want to use multiple origins, you must have a matching number of cache behaviors.

* Status codes in amazon cloudfront
* Always cached
  - 404: Not found.
  - 414: Request-URL too large.
  - 500: Internal server error.
  - 501: Not implemented.
  - 502: Bad gateway.
  - 503: Service unavailable.
  - 504: Gateway timeout.
* Conditionally cached
- Requires the use of the headers ('Cache-Control max-age', 'Cache-Control s-maxage').
  - 400: Bad request.
  - 403: Forbidden.
  - 405: Method not allowed.
  - 412: Precondition Failed.
  - 415: Unsupported Media Type.
 
### S3 Origins, Custom Origins, and Origin Access Identities in CloudFront

* Origin Types
  - Amazon S3
  - MediaStore/MediaPackage
  - Application Load Balancer
  - Lambda Function URLs
  - Amazon EC2 Instance
  - CloudFront Origin Group (Group origin types together)

* Origin Access Controls
  Origin access control (OAC) is the successor to an origin access identity (OAI).

  - Feature differences
    1. Using all Amazon S3 buckets in all AWS regions. Includes opt-in regions launched after December 2022.
    2. Amazon S3 buckets using AWS KMS (SSE-KMS).
    3. Dynamic requests (PUT and DELETE) to S3.
    
  - Reviewing
    1. Requires an Amazon S3 bucket origin that is NOT a website endpoint.
    2. Grant OAC access to bucket via the Amazon S3 bucket policy.
    3. Must grant access to OAC manually for distribution.
    4. OAC is a type of identity that is NOT a role or user.
  
* Application Load Balancer Custom Origins
  - CloudFront can cache objects from apps fronted by an ALB.
  - Allows inheriting built-in DDos protection of CloudFront as well.
  - Similar to S3 origins, you can restrict ALB access to your distributions.
  - Configure CloudFront to add custom HTTP headers to incoming requests.
  - Configure ALB to only forward requests containing a custom HTTP header.

* Amazon EC2 Custom Origins Security
  - Leverage operating system firewalls for controlling access.
 
### Securing Amazon CloudFront with TLS/SSL
* Five important cache behaviour settings
  - Precedence, the order of precedence for evaluating behaviours. Which is the default.
  - Path Pattern, request path pattern the cache behaviour will apply to eg. *.gif
  - Origin/Origin group, which origin/origin group the requests are routed to.
  - Viewer Protocol Policy, which protocol you want to use for edge locations eg. HTTPS only, Redirect HTTP to HTTPS, HTTP and HTTPS.
  - Restrict Viewer Access, Require use of signed URLs or signed cookies.

* CloudFront TLS/HTTPS overview
  - Standard CloudFront assigned domain name, included default TLS support, CNAMES are supported.

* CloudFront TLS/HTTPS details
  - Native ACM integration. Certs MUST be issued in us-east-1.
  - CloudFront TLS/SSL implementation have support for SNI.
  - SNI allows multiple hosts and certs using the same shared IP.
  - Non-SNI browser support is available, but it is very expensive.

* Architecture example
  
* Origin protocol differences
  - Amazon S3 origins automatically handle TLS certificates.
  - ALB origins require a public TLS cert (ACM-issued is most common).
  - Custom origins (EC2/on-premises) require external public TLS cert.
 
### Private Viewer Access in Amazon CloudFront
Private Viewer Access, we can restrict access to content using private viewer access.
Trusted key groups or trusted signers are required to generate these.

* Signed URLs, perfect for restricting access to specfic, individual files (specfic.exe). Useful when clients/users cannot support cookies.
* Signed Cookies, perfect for providing access to multiple restricted files (*.mp4). Do NOT want to change current URLs.
* Signed URLs always take precedence over signed cookies.

### Protect Sensitive Data with CloudFront Field-level encryption
* Field-Level Encryption, is an ability to add an additional layer of security for protecting data throughout a process/request within CloudFront.
  - Feature to help protect sensitive data within CloudFront.
  - Information is encrypted at the edge and remains encrypted throughout.
  - Completely separate from the actual HTTP/S tunnels.
  - Uses a public and private key pair for all encryption/decryption.

* Four High-Level Configuration Steps
  - Generate your public and private key pair.
  - Create field-level encryption profile.
  - Create field-level encryption config.
  - Link to a cache behaviour.

### Georestricting Viewer Access in Amazon CloudFront
Prevents users in specific geographic areas from accessing CloudFront-distributed content.
* CloudFront-Native Restriction, restricts all access to files associated with a distribution. Restricts at a country level. 
* Third-Party Restriction, restricts access to a subset of files. Usually allows for finer granularity than country-level restrictions.

* CloudFront restrictions are based on an allow list and deny list.
* Requests from restricting locations will receive a 403 Forbidden status.
* From AWS: Third-party database leveraged that offers 99.8% accuracy.
* Ability to return custom error messages from denied requests.

### Invalidating and Expiring Files within CloudFront

* Control Caching durations
  - By default CloudFront serves a cached file until the specified cache duration.
  - After expiration, origin fetch occurs to update cached data.
  - Cache hit; CloudFront has the latest version for immediate return. Returns a 304 not modified.
  - Cache miss; CloudFront retrieves latest version from origin. Returns a 200 OK.
  - Default TTL of 24hrs, CloudFront can evict less common files.
* Cache TTL Headers
  - Cache-Control max-age, specified in seconds how long browser will cache the file.
  - Cache-Control s-maxage, ""
  - Expires, specifies a date and time that the file will expire.
* File Invalidation
  Remove cached files to force origin fetch before specified object TTL. Invalidations occur at the distribution level.
  - Invalidate Files, specify the path you want to invalidate (URL/files/*.png)
  - File Versioning, provide a version name in files for best control (*_v3.png)
 
### Customizing Requests Using Lambda@Edge and Amazon CloudFront Functions

* Requests and Responses
  - Viewer request, from the client to the edge server
  - Origin request, from the edge server to the origin server
  - Origin response, from the origin server to the edge server
  - Viewer response, from the edge server to the client.
* Lambda@Edge Overview
  - An extension of the AWS lambda service, must be in us-east-1.
  - Node.js or Python functions executed at edge locations.
  - Scale automatically up to thousands of requests per second.
  - Process request closer to viewers for reduced latency.
  - Allows for interception of request and responses for customizations.
* CloudFront Functions Overview
  - Native functions that live entirely within CloudFront.
  - Can only be written in javascript.
  - Extremely similar use cases to Lambda@Edge.
  - Leverage CloudFront Functions and Lambda@Edge if desired.
  - Best latency- sub-millisecond startup and millions of requests per second.
* Use Cases
  - A/B testing, headers could be used to direct traffic to different origins.
  - User-Agent checks
  - Inspect cookies
  - Custom HTTP responses
  - Authorization checks, inspect authorization checks
  - Make external Network calls.
* Adding Security Headers
  - Strict transport security, X-Frame-Options
  - Content-Security-Policy, X-XSS-Protection
  - X-Content-Type-Options, Referrer-Policy

### Optimizing CloudFront Distributions

* Monitoring Activity
  - Cache statistics
    1. Total requests.
    2. Result types.
    3. Bytes transferred.
    4. HTTP status codes.
    5. Incomplete requests
  - Popular objects.
    1. Info about 50 most popular objects.
  - Top referrers.
    1. Info about top 25 referrers.
  - Usage.
    1. Data transferred.
    2. Requests by protocol and destination.
  - Viewers
    1. Devices.
    2. Browsers.
    3. Operating systems.
    4. Locations.
  - Default metrics.
    1. Requests.
    2. Bytes downloaded.
    3. Bytes uploaded.
    4. 4xx error rate.
    5. 5xx error rate.
    6. Total error rate.
  - Additional metrics.
    1. Cache hit rate.
    2. Origin latency.
    3. Error rate by status code.
  - Standard logs
    1. Logs request details to S3 bucket.
    2. Enabled per distribution.
    3. Best-effort logging, usually under an hour up to 24hrs.
    4. Cannot modify fields.
    5. No CloudFront charges, still s3 charges.
  - Real-Time logs.
    1. Log data sent to Amazon Kinesis data stream.
    2. Enabled per behaviour.
    3. CloudFront real-time log configuration.
       3.1 Sample rate
       3.2 Data fields to include.
       3.3 Data stream endpoint.
       3.4 IAM role.
* Improving Cache Hit Performance
  - Price class (by default use all edge locations, best performance)
    1. Distribution setting.
    2. Determines which edge locations serve your content.
  - OriginShield
    1. Origin setting
    2. Caching layer between regional edge caches and origins.
    3. Operates in regional edge cache regions.
    4. Incurs additional charges.
* Cache Key
  - Unique identifier for cached objects.
  - Generated from selected viewer-request content.
    1. HTTP headers.
    2. Query strings.
    3. Cookie content.
  - Default cache key includes
    1. Distribution FQDN.
    2. URL path of requested object.
  - Cached items are returned if requests geneerate an exact cache key match.
  - Cache misses generate origin requests.
  - Customizing cache keys
    1. Cache keys may be customized per behaviour.
    2. Legacy cache settings configured directly within behaviour.
    3. Cache policies, defined as service-level objects. Use AWS managed policies or create your own.
    4. Can have unintended consequences including.
       4.1 Duplicate object caching.
       4.2 Reduced cache hit rate.
       4.3 Increased  origin requests.
* Modifying Origin Requests
  - Different content from viewer requests.
    1. URL path.
    2. Request body.
    3. Required HTTP headers.
    4. Cache key values.
  - Modified via origin request policies.
    1. Optional.
    2. Separate from cache policies.
  - Use origin request policies to send data to application
    1. Implement simplier cache key policies.
    2. Improve cache hit ratio.
* Cached Content usage
  - Content served from cache until TTL expiration, default 24hrs.
  - Requests after expiration cause check for updated object at origin.
  - Expired content evicted if not frequently used.
* Min/Max/default TTLs.
  - Cache policy or behaviour-level setting.
  - Applies to all files within behaviour path.
* Cache control HTTP headers
  - Control expiration for individual files.
  - Controls browser caching.
  - Works with min and max ttl values.
* Updating Cached Content
  - Unexpired cached content will always be used even if newer versions exist at origin.
  - Extra steps are necessary to ensure all Edge locations return the latest content version.
  - Add invalidation to distribution.
    1. Evict cached content before TTL expiration.
    2. Specify URL path of file(s) to be evicted.
    3. May take time for all evictions to occur.
    4. First 1000 paths/month are free.
  - Use versioned files or directories.
    1. Update file/directory names.
    2. Update URLs in application.
    3. More control over content updates.
    4. More visibility over update progress.
    5. Can roll back to prior versions.
    6. Less expensive than invalidation.
* Edge Functions
  - Customize request and response processing.
    1. Modify HTTP headers.
    2. Redirect or rewrite URLs.
    3. Authentication/authorization.
    4. Normalize cache key values.
  - Code runs at edge location.
  - Associated with:
    1. Cache behaviours.
    2. Request/response events.
   
### Accelerating Workloads Using AWS Global Accelerator

* AWS global accelerator overview - is a service in which you create accelerators to improve the performance of your applications.
* Global accelerator concepts and terms
  - Accelerator directs user traffic to the optimal AWS endpoints.
  - Network zones, isolated units of unique physical infrastructure.
  - Listener, processes inbound connections based on ports and protocols.
  - Endpoint, resources that global accelerator directs traffic to.
* Two types of accelerators
  - Standard
    1. Network load balancers
    2. Application load balancers
    3. Amazon ec2 instances
    4. Elastic IP addresses
  - Custom routing
    1. VPC subnets with EC2 instances
* 10000 foot view
  - AWS provides two static IP addresses (Anycast) to associate with accelerators.
  - Dual-stack receives four static IP addresses (2 IPv4 and 2 IPv6).
  - Static IPs act as a single, fixed entry for ALL client traffic.
  - Standard, traffic routed based on user location, health checks and weights.
  - Custom, traffic routed to specified EC2 instances and ports in a VPC.
* Important concepts to remember and conclusion
  - Accelerators allow you to implement edge locations nearer to clients.
  - Standard and Custom accelerator types leverage Anycast IPs.
  - TCP connections terminated at GA accelerators.
  - Remember: TCP or UDP connections! not HTTP or HTTPS
  - Differentiator: Amazon CloudFront supports HTTP and HTTPS.

### Lambda within a VPC
* VPC for lambda
  - Lambda functions run in an AWS owned VPC by default.
  - Default networking allows for public internet access and invocations.
  - If you need private VPC resource access, you can deploy functions into your VPC.
* Requirements and Concepts
  - Hyperplane ENI - Lambda provides a managed resource called Hyperplane that allows connections to your VPC.
  - Security groups - You must assign and manage security group configurations for your Hyperplane ENIs.
  - NAT Gateway - Internet access for these functions requires a NAT gateway. No public IP addresses.
* Hyperplane ENIs
  - Provide NAT capabilities from the AWS lambda VPC to your custom VPC.
  - This NAT capability is known as VPC-to-VPC NAT (V2N).
  - V2N allows connections from AWS-owned VPC to your VPC, but NOT the other way around.
  - This does NOT replace VPC interface endpoints.
  - Functions can share Hyperplane ENIs if they use the same security groups and subnets.
* Use Cases and Scenarios
  - Lambda funtions requiring access to private RDS instances.
  - Access to EC2 hosted applications in private subnets.
  - Security requirements to restrict any internet access from all AWS services in use.
* Conclusion
  - You can configure AWS lambda functions to gain VPC access.
  - Hyperplane ENIs are created in each chosen private VPC subnet.
  - V2N provides access from the AWS owned VPC to your VPC. One-way.
  - Functions can share Hyperplane ENIs if security groups and subnets are the same.
  - NAT gateway access is required to provide internet capabilities.

### Running Kubernetes in AWS
* AWS EKS Networking Overview
  - Always created within a VPC.
  - Clusters require a VPC with at least two subnets in different AZs.
  - VPC DNS hostname and resolution support must be enabled.
  - Subnets must have a minimum of six IP addresses (16 recommended).
  - EKS integrates with other AWS services. Primarily focus on ELBs.
* Load balancing
  - Application Load Balancer
    1. Kubernetes ingress.
    2. Layer 7.
    3. Configures routes of HTTP/S traffic.
  - Network Load Balancer.
    1. Service type of LoadBalancer.
    2. Layer 4.
    3. Pods on EC2 IP and instance targets.
* Conclusion
  - AWS managed VPC for Amazon EKS control plane infrastructure.
  - VPC kube-api traffic routed between managed ENIs in your VPC.
  - EKS admin access endpoint is publicly accessible by default.
  - ELB integrations, ALB (ingress) and NLB (Loadbalancer service type).
  - Custom VPC requires at least 2 subnets, with at least 6 IP addresses each.

### AppStream 2.0
* What is Amazon AppStream 2.0
  - Fully managed app streaming service for accessing desktop apps from anywhere
  - Delivers applications through web browsers that are HTML5 compatible.
  - Requires at least one subnet to run in. Fault tolerance needs two+ subnets in different AZs.
  - Each instance has an ENI in a VPC. A new instance is used for each individual user.
  - Network access is provided via the ENI security groups.
  - AppStream User Pools
  - Active Directory/SAML 2.0
* Use cases
  - Enterprise applications, data and applications can be centralised, and applications can be updated once.
  - Design and engineering, gives designers the ability to use powerful 3D applications on an device.
  - Online Trials/Training, quickly and easily trial and test software without downloading software locally.
* Possibilities for internet access
  - >100 concurrent users, new VPC and private subnets using a NAT gateway.
  - <100 concurrent users, new or existing VPC with public subnets with IGW access.
  - <100 concurrent users, default VPC and subnets.
* Networking notes
  - AppStream 2.0 uses S3 for storage depending on the configuration.
  - Easily leverage S3 VPC endpoints for secure access to buckets.
  - Customer ENI provides access to VPCs, the internet, and joining directories.
  - Management ENI, connects to secure management network and used for interactive streaming to user devices.
  - AppStream uses an IP from the 198.19.0.0/16 range for the management ENI.
  - Users connect to streaming instances via public internet endpoint or via VPC interface endpoints.
* User device required ports: internet endpoints
  - 443 Used for HTTPS communication between user devices and instances.
  - 8433 UDP HTTPS between user device and instances.
  - 53 Used for user devices and your DNS servers.
* ENI IP ranges and ports
  - Management
    1. Inbound TCP on port 8300, for establishment of the streaming connection.
    2. Inbound TCP on ports 8000 and 8443, for management of the streaming instance by AppStream 2.0
    3. Inbound UDP on port 8300, for establishment of the streaming connection over UDP.
  - Customer
    1. TCP 80
    2. TCP 443
    3. UDP 8433
    4. ENI IP Ranges and Ports joining directories.
      - TCP/UDP 53 DNS
      - TCP/UDP 88 Kerberos authentication
      - UDP 123 NTP
      - TCP 135 RPC
      - UDP 137-138
      - TCP 139 Netlogon
      - TCP/UDP 389 LDAP
      - TCP/UDP 445 SMB
      - TCP 1024-65535 Dynamic ports for RPC 

### App Networking via AWS App Mesh
* Overview
  - AWS managed service mesh for monitoring and controlling services.
  - Perfect use for complex microservice architecture within AWS.
  - Based on Envoy proxy, the open-source project.
  - App Mesh deploys a validated  Envoy proxy container image.
  - Management port is available to all hosts within a VPC.

### Securing API Gateway
* Options
  - REST API, API keys, per-client throttles, validation of requests, WAF integration.
  - HTTP API, Simpler option than REST API, cheaper, minimal features.
  - Websocket API, collection of websocket routes integrated with lambda functions, http endpoints, and other AWS services.
* Securing published REST APIs
  - Support for custom domain names that allow you to use your own URL.
  - Regional custom domain names require ACM certs in their region.
  - Use a CloudFront distribution to configure an edge-optimised domain name.
  - Edge-optimised custom domain names require ACM certs in us-east-1.
  - Leverage Route53 health checks to support backup region DNS failovers.
* Changing endpoint types
  - From edge-optimised to regional or private.
  - From regional to edge-optimised or private.
  - From private to regional.
  - Cannot go from private API to edge-optimised endpoint.

### Enabling Enhanced Networking on Amazon EC2
* Traditional virtual networking
  - All networking is virtual and shares the same physical hardware.
  - Controlled by the underlying hypervisor software.
  - Original compensation effort was to enable VM networking passthrough.
* SR-IOV
  - Single root I/O virtualisation.
  - Supports very high performance networking requirements.
  - Drastically lower CPU utilisation compared to traditional virtual networking.
  - Benefits, higher bandwidth, higher packet per second (PPS) performance, and consistently lower inter-instance latencies.
  - No additional charge to enable this service.
  - All current generation instance types (excluding T2) support this.
* Enhanced networking interface types
  - Elastic Network Adapter (ENA), network speeds up to 100 Gbps.
    1. Requires installation of ENA module and needs to have ENA support enabled.
    2. Most common implementation for current generation of instances.
    3. ENA Express for high-performance communication of instances in the same subnet.
  - Intel 82599 Virtual Function (VF), network speeds up to 10 Gbps.
    1. Uses the Intel ixgbevf driver.
    2. Supported instance types: C3, C4, D2, I2, M4 and R3.
* Multi-flow vs single-flow traffic
  - Multi-flow (MPTCP)
    1. Aggregated instance bandwidth dependent on destination of traffic.
    2. Same-region traffic utilises full bandwidth that is available.
    3. Cross region, IGW and DX traffic utilises up to 50% of network bandwidth available.
    4. Instances with less than 32vCPUs limited to 5 Gbps.
  - Single-flow, 5-tuple traffic
    1. Source IP, Source Port, Destination IP, Destination Port and Protocol.
    2. Limited to 5 Gbps for instance NOT in the same cluster placement group.
    3. Workaround is to enable ENA Express to achieve up to 25 Gbps between instances in the same subnets.

### High Performance Computing with Elastic Fabric Adapter
* EFA
  - Elastic fabric adapter is a network device that can be attached to EC2 instances to accelerate high performance computing and machine learning apps.
* Overview
  - More consistent latency and higher throughput than most HPC systems.
  - Enhance the performance of inter-instance communications.
  - Integrates with Libfabric 1.7.0 and later.
  - ENA with additional capabilities (not fully supported with Windows).
  - Offers OS-bypass. HPC and ML apps directly communicates with NIC hardware.
  - Supports Open MPI, Intel MPI and NCCL.
  - Uses OP TCIP/IP stack and ENA driver to enable communication.
  - Libfabric API bypasses OS kernels and directly talk to EFA device.
  - Greatly reduces overhead.
  - EFA OS-bypass traffic limited to a single subnet.
  - Security groups must allow all inbound/outbound traffic to and from itself.
