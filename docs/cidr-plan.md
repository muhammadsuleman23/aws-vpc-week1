# CIDR and Subnet Plan

VPC: `my-vpc` (`vpc-048c58f6dec640913`), IPv4 `10.20.0.0/16`, region us-east-1.
The default VPC uses `172.31.0.0/16`, so there is no overlap with it.

| Name | Type | CIDR | AZ | Usable hosts | Route table |
|---|---|---|---|---|---|
| Public-1 | Public | 10.20.1.0/24 | us-east-1a | 251 | public-rt |
| Public-2 | Public | 10.20.2.0/24 | us-east-1b | 251 | public-rt |
| Private-1 | Private | 10.20.11.0/24 | us-east-1a | 251 | private-rt |
| Private-2 | Private | 10.20.12.0/24 | us-east-1b | 251 | private-rt |

AWS reserves 5 addresses per subnet, leaving 251 usable of the 256 in each /24.

## Numbering logic

- `10.20.1.x`, `10.20.2.x` are the public subnets.
- `10.20.11.x`, `10.20.12.x` are the private subnets (the 10 offset makes the tier obvious).
- Large unused space remains in `10.20.0.0/16` for future tiers (for example a database subnet).

## Placement

Each tier has one subnet in each of two Availability Zones, so the design survives the loss of a single AZ.

## Instances

| Instance | Subnet | Private IP | Public IP |
|---|---|---|---|
| `public-ec2` (`i-0a4bc774031f88ae7`) | Public-2 | 10.20.2.117 | Auto-assigned |
| `private-ec2` (`i-0db2f13517d41cefd`) | Private-1 | 10.20.11.81 | None |

Evidence: `screenshots/01-vpc-resource-map.png`, `screenshots/04-private-route-table-associations.png`.
