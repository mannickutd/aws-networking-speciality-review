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

### Demo: Web Application Firewall

### Traffic Protection

### Demo: AWS Certificate Manager

### Traffic Awareness

###
