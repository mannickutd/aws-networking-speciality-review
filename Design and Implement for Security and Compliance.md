### Traffic Control
* AWS provides a number of built-in and optional features and services residing at edge locations and Regions that can be used to identify both legitimate and unwanted traffic.
* Filtering traffic
  - Incoming network traffic can be filtered in serveral ways
    1. Filtering by traffic properties.
    2. Filtering by requestor identity.
  - Filtering by destination is controlled by route tables.
* AWS Shield
  - Managed DDos protection service.
  - Hosted at all CloudFront, Global Accelerator, and Route 53 edge locations.
  - Covers approximately 96% of known layer 3 and 4 attacks.
  - Standard is automatically provided to all AWS customers at no charge.
  - Advanced is a paid service that provides.
    1. Additional protection for select services.
    2. Detailed monitoring.
    3. WAF at no charge.
    4. 24x7 DDoS response team.
    5. EDoS (economic denial of sustainability) coverage\
* AWS Web Application Firewall (WAF)
  - Allow, deny, or count HTTP/HTTPS requests using ACLs.
  - Applicable to API Gateway, CloudFront, and Application Load Balancer.
  - New version of AWS WAF released in November 2019.
  - ACL Components
    1. Conditions are used to identify request content of interest, eg. cross-site scripting, country of origin, ip address, size of request properties, sql queries, string/regex.
    2. Each condition contains one or more filters.
    3. Multiple condition filters are OR-ed.
  - Rules contain one or more conditions
    1. Specify if traffic should match or not match condition filters.
    2. Multiple conditions are AND-ed.
  - Rules can be normal or rate-based.
  - Customers may purchase managed rule sets to be used alongside customer-created rules.
  - ACLs contain one or more rules
    1. Rules are set to either allow, deny or count matching requests.
    2. Multiple rules are processed in the order listed in the ACL.
    3. Default action applies to requests that match no rules.
  - Rate-Based rules only apply their action after a set number of requests are received.
  - ACLs may be associated with ALBs, API Gateway APIs and CloudFront distributions.
  - While an ACL remains associated, it may only be further associated with other objects of the same type.
  - ALB and API associations become region-restricted.
  - Pricing
    1. Per ACL, per month.
    2. Per Rule, per month.
    3. Per 1 million requests.
    4. Managed rule prices are set by the seller.
  - ACL Quota Limits
    1. Per account, per region.
      a. 50 ACLs.
      b. 100 rules.
      c. 5 rate-based rules.
      d. 100 of each condition type except
      e. 10 regex conditions (cannot be increased).
    2. Maximum of 10 conditions per rule.
    3. Maximum of 10 rules per ACL.
* Route 53
  - Public-hosted zones are public.
  - Use private-hosted zones for internal use.
  - Geolocating routing policies can deflect name-resolution requests from selected areas.
* CloudFront
  - Content in CF distributions are inherently public.
  - Ensure all traffic is sent to CF distribution and not to origin data source.
    1. Resolve traffic to distribution DNS name.
    2. S3 bucket origins - restrict traffic using CF Origin Access Identity.
    3. Custom origins - only accept requests that include signed URLs, cookies, or custom headers added by CF distribution.
  - CF distributions support geo restriction.
    1. Choose either whitelist or blacklist countries.
    2. Optional custom error message for blocked requests.
* S3
  - Avoid enabling public access.
  - Use the correct type of authentication for your authorizations.
    1. IAM permissions require IAM authentication.
    2. Bucket policies and ACLs do not require IAM authentication.
  - Require signed URLs.
  - Use bucket policy conditions.
  - S3 service is natively accessed via the internet.
    1. VPC gateway endpoints allow access from VPCs in the same region.
  - Access can be restricted by both S3 bucket policies and gateway endpoint policies.
  - S3 Access Points allow the creation of customized access points for an S3 bucket.
  - Each Access Point
    1. Has a unique host name.
    2. Has distinct permissions and network controls.
    3. Can be limited to VPCs.
  - Can be managed by AWS Organizations SCPs (Service Control Policies
* VPC NACLs and Security Groups.
  - NACLs:
    1. Applied to subnets.
    2. Only one per subnet.
    3. Rules processed in sequence
    4. Have explicit deny.
    5. Only recognize IP traffic properties.
    6. Stateless.
  - Security Groups
    1. Applied to ENIs.
    2. Up to five per ENI.
    3. Rules processed collectively, they're ORed together.
    4. No explicity deny.
    5. Can identify source and destination security groups.
    6. Stateful.
* Elastic Load Balancer
  - Only accepts traffic matching a configured listener.
  - Protected by VPC security groups.
* ALB built in Authentication
  - Authentication can be offloaded from the application to ALB.
  - Traffic matching a listener rule is sent to a configured identity provider (IdP).
  - Supported methods include:
    1. OIDC-compliant IdPs.
    2. SAML.
    3. Well-known social IdPs.
    4. LDAP.
    5. Amazon Cognito user pools.
    6. Microsoft AD.
* EC2 and Container Instances.
  - Limited only by what the instance OS can support.
  - May provide features that comparable, AWS-managed services do not.
  - Services hosted on instances are the customer's responsibility to maintain.

### Traffic Protection
* Secure data transmission requires:
  - Verifying the identity of the receipient.
  - Encrypting transmitted data.
* Public Key Infrastructure (PKI)
  - PKI is a collection of systems used to verify identities and secure electronic data transfer.
  - Certificates are used by PKIs to verify identities and ownership of encryption keys.
* Certificates
  - Certificates contain information to validate the identity of both the holder of the certificate as well as the issuer of the certificate
    1. Name of the certificate holder.
    2. Other information about the holder.
    3. Purpose of the certificate.
    4. Expiration date.
    5. Identity of the issuer.
    6. Means of validating the authenticity of the certificate.
  - Trust in the issuing certificate authority (CA) chain is required.
  - Servers use certificates used by public CAs to prove their identity to requesting clients.
  - If there are any problems in validating the certificates in the chain, the associated operation will be rejected.
* SSL/TLS (Secure Sockets Layer/Transport Layer Security)
  - SSL/TLS are protocols used to establish secure network communications.
  - SSL depreciated in 2015.
  - AWS recommends using version SSL 1.2 as a minimum.
* ACM (AWS Certificate Manager)
  - Certificates in AWS can be managed using either ACM or IAM.
    1. ACM not available in all regions.
    2. IAM cannot be used to create certificates.
    3. ACM certificates cannot be imported by IAM.
    4. IAM certificates cannot be managed from the console.
  - Integrated with AWS Services.
    1. ELB.
    2. CloudFront.
    3. API Gateway.
    4. CloudFormation.
    5. Elastic Beanstalk.
  - Provision public certificates.
    1. SSL/TLS for public services.
    2. Import external X.509 certificates.
    3. Provisiioned from AWS CA.
    4. Free service.
  - Requires validation of domain ownership.
    1. FQDN of website (* wildcard accepted).
    2. Validate via:
       a. DNS (Add text record to zone).
       b. Email (automatically sent to zone contacts).
    3. Certificate format is X.509 v3.
    4. Valid for 13 months.
  - Certificates are provisioned on a per-region basis.
  - Certificates used by CloudFront must be provisioned in us-east-1.
  - ACM certificates can only by used by AWS services integrated with ACM.
  - ACM certificates and their private keys may not be downloaded.
* AWS Certificate Manager Private Certificate Authority (ACM PCA).
  - Create your own private CA infrastructure.
    1. SSL/TLS certificates to identify internal resources.
    2. $400/month per CA.
    3. Charge per private certificate.
* What needs to be protected?
  - Requests from external clients to AWS services.
  - Traffic within AWS.
* Protecting client requests - CloudFront
  - Settings configured per cache behaviour.
  - Viewer Protocol Policy
    1. HTTP and HTTPS (default)
    2. Redirect HTTP to HTTPS
       a. Tells client to submit new request to HTTPS URL.
       b. Requests below HTTP v1.1 will be rejected (status 403 Forbidden).
  - SSL Certificate
    1. Default CloudFront certificate.
      a. Requests must use CF domain name.
      b. Only supports TLSv1 or later.
    2. Custom SSL certificate.
       a. Requests use custom domain name.
       b. Certificates must be in either ACM (us-east-1) or in IAM.
  * Protecting client requests - S3
    - Deny connections not using HTTPS via bucket policy condition.
  * Protecting client requests - ELB
    - Configure a listener for a secure protocol.
      1. CLB - HTTPS or SSL.
      2. ALB - HTTPS only
      3. NLB - TLS only
    - Select default certificate and security policy.
      1. CLB may modify policy details in console.
      2. ALB and NLB may add additional certificates after creation.
      3. Default certificate is used when
         a. Client doesn't connect with SNI support.
         b. Requested hostname does not match any additional certificates.
  * Protecting client requests - EC2 and Container Instances.
    - Enabling HTTPS/TLS at ELB terminates secure session at ELB.
    - Configure CLB/NLB listener for TCP.
    - Route traffic to appropriate port on application instance.
    - Services hosted on instances are customers responsibility to configure and maintain.
    - Handling SSL/TLS connections at application server increases resource consumption.
  * Protecting Intra - AWS Traffic
    - AWS network traffic uses shared infrastructure.
    - AWS service objects provisioned in AWS may be hosted on hardware in different locations.
    - Organizational compliance requirements might require secured connections to customer managed services.
  * Protecting Intra - AWS Traffic CloudFront
    - Settings configured per origin.
    - Origin Protocol Policy.
      1. HTTP Only (default)
      2. HTTPS Only.
      3. Match Viewer.
    - Origin-type specifics
      1. Certificate at custom origin servers must be from Mozilla-trusted CA.
      2. S3 bucket origins are always "Match Viewer", configure viewer protocol policy.
      3. S3 buckets as websites do not support HTTPS.
  * Protecting Intra - AWS Traffic EC2 and Container Instances.
    - Secured network connections in-between instances require OS-level service configuration.
    - EBS encryption encrypts data traffic between instance and EBS volumes.
    - EFS traffic can be secured by TLS when volumes are mounted (per connection)
    - FSx traffic is automatically encrypted when accessed using SMB 3 or Samba 4.2 (or newer)
  * Protecting Intra - AWS Traffic RDS
    - RDS creates an SSL/TLS certificate and installs the certificate on the provisioned DB instance.
    - Methods of requiring SSL/TLS connections vary by DB engine and version.
    - AWS root certificates for RDS will need to be applied to clients.
  * What about Encryption at Rest?
    - Data can be encrypted at different locations.
      1. Client.
      2. AWS service.
      3. Customer service.
    - AWS services for customer key management.
      1. Key Management Service (KMS)
         a. Integrated with many AWS services.
         b. Allows granular access controls.
         c. Shared infrastructure.
      2. CloudHSM
         a. AWS-managed HSM appliance.
         b. Not integrated with AWS services. 

### Traffic Awareness
* Awareness
  - Traffic-related events.
  - Performance-related events.
  - AWS Event-related events.
  - Security-related events.
* Amazon CloudWatch and Amazon EventBridge
  - Metrics: Performance numbers and counts.
  - Logs, filterable information with possibility for greater detail.
  - Events, leverage EventBridge to automate responses to criteria.
  - The unified CloudWatch agent allows for custom metric collections.
* Amazon CloudWatch and Web Application Firewall Metrics
  - Set up separate metric namespaces for each rule and ACL.
  - AllowedRequests, BlockedRequests, CountedRequests, PassedRequests.
  - Metrics for ELB/API-associated ACLs are viewed in the object's Region.
  - Metrics for CloudFront are viewed in us-east-1.
* Amazon CloudWatch and Shield Advanced Metrics.
  - There are a few general metrics you can and should monitor.
    1. DDoSDetected
    2. DDoSAttackBitsPerSecond
    3. DDoSAttackPacketsPerSecond
    4. DDoSAttackRequestsPerSecond
  - DDoSAttack, per-second metrics can be viewed by AttackVector dimensions.
  - CloudFront and Route53 metrics are viewed in us-east-1.
* Amazon CloudWatch and VPC Traffic Mirroring Metrics
  - These are reported by monitored EC2 isntances.
    1. NetworkMirrorIn/Out
    2. NetworkPacketsMirrorIn/Out
    3. NetworkSkipMirrorIn/Out
    4. NetworkPacketsSkipMirrorIn/Out
  - NetworkPackets from above are available for basic monitoring only.
* Instance-Level Services
  - Packet Capture and Analysis
  - Intrusion Detection/Prevention
  - ENI can be a target for VPC traffic mirroring sessions.
    1. Packet capture software must support VXLAN decapsulation.
  - IDS monitors network and reports events.
  - IPS can modify traffic or service configurations in response to events.
  - Often sandwiched between ELB layers.
  - Service appliances are avilable in the AWS marketplace.
* Automation
  - CloudWatch Alarms and Filters, use CloudWatch alarms and event filters to recognise circumstances of interest.
  - Remediation and Notifications, use Lambda functions and SNS topics to trigger notification and response actions.
* Visualize and Analyze
  - Amazon Kinesis Data Streams can be used to stream real-time data.
  - Amazon OpenSearch can be used to perform searches and analytics of real-time or static data.
  - Amazon Athena can be used to query data in S3 using SQL.

### Copying Network Traffic with VPC Traffic Mirroring
* Traffic Mirroring
  - Traffic Mirroring, VPC feature that allows you to copy network traffic from an ENI and then sends that traffic to an out-of-band security or monitoring appliance.
* Traffic Mirroring Use Cases
  - Content Inspection
  - Threat Monitoring
  - Troubleshooting
* Traffic Mirroring Concepts
  - Source, which ENI you want to monitor.
  - Filter, the set of rules that are defining what traffic is actually monitored.
  - Target, the configured destination for your mirrored traffic (ENI, NLB or GWLB)
  - Session, the establishment of relationships between sources, filters and targets.
    1. NLB or GWLB Targets, you might get out-of-order mirrored packets.

### Managed IDS with Amazon GuardDuty
* GuardDuty concepts
  - AWS managed, continuous security monitoring and intrusion detection service.
  - Leverages threat intelligence feeds and machine learning to identify and alert.
  - Easily generate and report on threat insights around AWS accounts/organizations.
  - Use GuardDuty in AWS Organizations to easily centralize findings and reporting.
* Examples
  - Escalated privileges.
  - Exposed credentials.
  - Malicious IP address interactions.
  - Cryptocurrency mining and activities.

### Inspecting Compute Networking with Amazon Inspector
*  Overview
  - Managed security service with automated vulnerability/access scanning.
  - Delegated administrator allows you to centralize findings with ease.
  - Two types of scans, Network Assessment, Host Assessment (SSM Agent)
*  Network Assessments
  - scans compute for network reachability (EC2 resources and Lambda)
  - Performed on an automated schedule (every 24hrs)
  - Agentless version of assessment.
  - Reports on overly permissive/open networking configs (Security Groups, NACLs etc)

### DDoS Protection Using AWS Shield
* Overview
  - Service offering protection against DDoS attacks targeting AWS resources.
  - Protection for Layer 3, Layer 4, and Layer 7.
  - Two options, standard (free) and advanced (paid)
* AWS Shield Standard
  - The free version is enabled automatically for all AWS customers
  - Handles most of the common attacks.
  - Limitations in resources, including Route 53, CloudFront, and standard AWS Global Accelerator endpoints.
* AWS Shield Advanced
  - Hefty price tag, $3k per month, with one year commitment.
  - Greater protection offerings. DDoS, volumetric bot attacks, exploits.
  - More supported resources, including EC@ EIPs, ELBs and EC2 instances.
  - Centrally manage via AWS organizations and AWS Firewall Manager.
  - Comes with a dedicated support team and financial insurance.

### VPC Perimeter Protection via AWS Network Firewall
* AWS Network Firewall
  - Stateful network firewall service, used for IDS and IPS within VPCs.
  - Filters traffic at perimeters of your VPCs. Catches traffic going to and from IGW, NAT Gateways, VPNs and even Direct Connections.
  - Used for domain name filtering, blocking bad domains, and packet inspection.
  - Allows for smart protocol detection. Eg. Look for HTTPS not just at port 443.
  - Always deploy firewall endpoints into their OWN subnets. It cannot inspect traffic within itw own subnet.
* AWS Network Firewall Components
  - Firewall, connects to the actual VPC that you want to protect. Protects inbound and outbound traffic flows.
  - Firewall policy, defines the behaviours of the firewalls. Only one firewall policy per firewall, but multiple firewalls per policy is allowed
  - Rule Group, collection of both stateful and stateless rules for inspecting and handling traffic flows.
* Rule Groups - Stateless
  - Defines standard network connection attributes for examining a packet on its own, with no additional context.
  - This means no knowledge of traffic direction, existing connections etc.
  - 5-tuple criteria
    1. Protocol.
    2. Source IP.
    3. Source port.
    4. Destination IP.
    5. Destination port.
  - Rules get processed in the prioritized order. Low to High and first match wins!
  - Three Actions:
    1. Pass.
    2. Drop.
    3. Forward.
  - Comparable to NACLs but more advanced
* Stateless Rule Actions
  - Pass, discontinue all inspection of the packet, and permit it to go to its intended destination.
  - Drop, discontinue all inspection of the packet, and block it from going to its intended destination.
  - Foward to stateful rules, discontinue stateless inspection of the packet, and forward it to the stateful rule engine for inspection.
* Rule Groups - Stateful
  - Inspects the incoming packets WITH context of flow and traffic information.
  - Compatible with suricata rules. Can be imported as well.
  - Support for, standard rules, suricata rules, and domain list rules.
  - Rules processed in order of the action settings. Stops on first match Pass > Drop > Alert.
  - Default behaviour is to allow all traffic to pass.
  - These are similar to security groups.
* Stateful Rules
  - Domain List Rule, allow and deny based on domain names, including protocols. Uses SNI for HTTPS hostnames.
  - Suricata Rule, this rule type contains actions, header and optional settings.
  - Standard Rule, translated into suricata rules.

### AWS CloudHSM Overview
* Overview
  - Highly available HSM clusters within AWS. Single-tenant solution for cryptographic keys.
  - AWS hosts the clusters themselves.
  - ENIs are deployed to your chosen VPC subnets.
  - FIPS 140-2 Level 3 compliant hardware.
  - Access exclusively via industry-standardized API calls and libraries.
* Networking aspects and uses
  - Automatically load balanced when using more than one node.
  - Offload cryptographic computation burden of TLS from web infrastructure.
  - Enable Oracle TDE and store master encryption key in HSM clusters.
  - Store your own Certificate Authority (CA) private key.
  - AWS-owned AWS account hosts the actual clusters. ENIs are deployed into your chosen VPC and subnets for interaction.

  ### Central Firewall Management with AWS Firewall Manager
  * AWS Firewall Manager, service that simplifies administration and maintenance tasks across multiple accounts and resources for a variety of protections.
  * Simplifying the administration of tools such as
    - AWS WAF.
    - AWS shield advanced.
    - Amazon VPC security groups.
    - AWS Network Firewall.
    - Amazon Route 53 Resolver DNS Firewall.
    - Palo Alto Network Cloud NFGW
    - Fortigate Cloud Native Firewall (CNF) as a service.
  * Concepts
    - Resource Set, collection of resources, such as firewalls that you can group together and manage in a Firewall Manager policy.
    - Firewall Manager Policy Common Settings, several common settings across many different resources. These include a name, the scope of the policy, and resource tagging to control policy scope.
    - Findings, creates findings for resources that are out of compliance and for attacks that it detects and sends them to AWS Security Hub.
   
### Governance and Compliance
* Overview
  - Traffic control, let in desired traffic and restrict the rest.
  - Traffic protection, secure appropriate traffic sessions.
  - Traffic awareness, create automated monitoring and response procedures.
* How can we verify the our environment is configured according to plan? How can we ensure that our environment won't be inappropriately modified.
  - Governane, establishing systems to ensure that organizations are following the "rules".
  - Compliance, demonstrating that organizations are in fact, following the "rules".
  - Organizations establish procedures to
    1. Ensure objectives are efficiently met.
    2. Ensure risks are identified and mitigated.
* AWS IAM
  - Access control to AWS services.
  - All AWS API calls must be authenticated and authorized.
* AWS Organizations
  - Allows centralized management of multiple AWS accounts.
  - Apply Service Control Policies (SCPs) onto child accounts to define the maximum applicable IAM permissions.
    1. Child account principals may only perform actions allowed by both AWS account IAM policies and Organizational SCPs.
* AWS CloudTrail
  - AWS API-call auditing service.
  - Audit trails from multiple accounts can be sent to a single S3 bucket.
  - Integrated with CloudWatch Events.
  - Does NOT monitor network traffic.
* AWS Config
  - Monitors the configuration of your AWS resources.
  - Tracks configuration changes.
  - Applies remediation rules.
  - Integrates with CloudTrail.
* Goverance
  - Follow IAM best practices
    1. Secure the root user.
    2. Use multi-factor authentication.
    3. Authorize roles instead of users.
    4. Grant least privilege.
  - Enforce organizational policies with AWS Organization SCPs.
  - Automate responses to audited API calls using CloudWatch Events.
  - Automate configuration change remediation with Config rules.
  - Investigate the cause of events to determine follow-up actions.
  - Require that common resources be created using CloudFormation templates.
    1. CF authorized to manage resources via IAM role.
    2. Users only given permission to use CF templates.
* AWS Service Catalog
  - Controls and standardizes deployment of AWS services.
  - Catalog administrators define products using CloudFormation templates.
  - End users may deploy products that they have been granted access to.
* AWS Firewall Manager
  - Provides AWS Organizations an interface to centrally enforce deployment of
    1. WAF ACLs
    2. AWS Shield Advanced protection policies.
    3. VPC security groups.
  - Reports findings to AWS Security Hub (if enabled)
* AWS Security Hub
  - Centralized security and compliance monitoring service.
  - Gathers data from AWS and supported third-party products.
  - Consolidates information across multiple accounts.
  - Runs account configuration and compliance checks.
  - Imports findings from Amazon GuardDuty and Amazon Inspector.
  - Receives findings from AWS Firewall Manager.
    1. WAF policy non-compliance
    2. AWS Shield Advanced not protecting resources.
    3. AWS Shield Advanced identifies an attack.
    4. VPC Security Group configuration issues.
  - Integrates with CloudWatch Events.
