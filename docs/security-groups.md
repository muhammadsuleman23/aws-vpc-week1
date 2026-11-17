# Security Groups (Least Privilege)

## public-sg (`sg-03ca98cf6a62c6e4b`) for `public-ec2`

| Direction | Type | Protocol / Port | Source | Reason |
|---|---|---|---|---|
| Inbound | SSH | TCP 22 | **My IP /32** | Administration only from the student's own address; never `0.0.0.0/0` |
| Inbound | HTTP | TCP 80 | `0.0.0.0/0` | Reserved for an optional web-server test; no web service was deployed or used in the tests |
| Outbound | All | All | `0.0.0.0/0` | AWS default |

Evidence: `screenshots/05-public-sg-inbound-rules.png`. The SSH source is a single `/32` address (hidden in the screenshot).

## private-sg (`sg-0e7dac6a730b84a17`) for `private-ec2`

| Direction | Type | Protocol / Port | Source | Reason |
|---|---|---|---|---|
| Inbound | SSH | TCP 22 | `public-sg` | Only the public instance (bastion role) may SSH in |
| Inbound | All ICMP - IPv4 | ICMP | `public-sg` | Allows ping from the public instance for connectivity testing |
| Outbound | All | All | `0.0.0.0/0` | AWS default (no route to the internet exists anyway) |

The sources are **security groups**, not CIDR ranges, so access follows the instance, not an IP address. Nothing on the internet can reach `private-ec2`.

Verified functionally: SSH and ping from `public-ec2` to `private-ec2` succeed (`screenshots/09-public-to-private-ping-and-ssh.png`).

## Key handling

The `.pem` key stays on the student's laptop. Access to the private instance uses **SSH agent forwarding** (`ssh -A`), so the private key is never copied to the public instance, and keys are excluded from Git by `.gitignore`.

## Instance hardening notes

- IMDSv2 is **required** on both instances.
- Only `public-ec2` has a public IPv4 address.
