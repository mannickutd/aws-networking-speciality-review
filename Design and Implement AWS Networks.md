# Design and Implement AWS Networks

## AWS Global infrastructure

| Infrastructure | Role | Notes |
| -------------- | ---- | ----- |
| Regions | Provide geographical locations in the world where AWS operates services. | Regions are completely independent of other regions. |
| Availability zones | Provides a fast connection to other zones in the same region. | Consists of one or more data centers within a region. AZ's have 'a', 'b', 'c' appended onto them. |
| Edge locations | Deliver content to end users with low latency. | A content distribution network where data is cached. The backbone of cloud front. |

## VPC Basic Networking Design

VPC - A VPC lets you provision a logically-isolated section of the AWS cloud. You have complete control over your virtual networking environment, selection of IP address range, creation of subnets, and configuration of routing tables and network gateways.

IPv4 Number of IPv4 address
2^(32 - slashSubnetting)
EG. Number of IPv4 address in /24?
2^(32-24) = 2^8 = 256

NOTE: AWS will take 5 IP addresses for its own use whenever a VPC is created.


| IPv4 Addressing | IPv6 Addressing |
| --------------- | --------------- |
| Required for all VPCs | Optional |
| You have a choice of size between /16 to /28 CIDR block. | Has a fixed size of /56 CIDR block. |

