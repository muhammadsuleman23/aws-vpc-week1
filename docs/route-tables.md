# Route Tables and Routing Path per Subnet

| Route table | ID | Routes | Associated subnets |
|---|---|---|---|
| `public-rt` | `rtb-00b10aeda94ed4cd7` | `10.20.0.0/16 -> local`, `0.0.0.0/0 -> igw-00d15e150115a18eb` (suleman-igw) | Public-1, Public-2 (explicit) |
| `private-rt` | `rtb-0bc1295787d398e3d` | `10.20.0.0/16 -> local` | Private-1, Private-2 (explicit) |
| Main route table | `rtb-0012935b0bee31b27` | Created by AWS with the VPC; no subnet is relied on for routing | none used |

Evidence: `screenshots/03-public-route-table-routes.png`, `screenshots/04-private-route-table-associations.png`, `screenshots/02-internet-gateway-attached.png`.

## Routing path per subnet

### Public-1 and Public-2
- Traffic to `10.20.0.0/16` stays inside the VPC (`local`).
- Everything else (`0.0.0.0/0`) goes to the **Internet Gateway** `suleman-igw`.
- Instances with a public IPv4 address can therefore reach the internet and be reached (subject to security groups).

### Private-1 and Private-2
- Traffic to `10.20.0.0/16` stays inside the VPC (`local`).
- There is **no default route**: no Internet Gateway route and no NAT route.
- Private instances cannot reach the internet and cannot be reached from it.

## Optional future change (requires instructor approval)

Create a NAT Gateway in a public subnet and add `0.0.0.0/0 -> nat-gateway` to `private-rt`. This gives private instances outbound-only internet access for updates. It is chargeable, so it was not created.

## Internet Gateway

`suleman-igw` (`igw-00d15e150115a18eb`) is attached to `my-vpc`, state **Attached**.
