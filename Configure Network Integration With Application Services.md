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




