# Connectivity Tests

Source, destination, protocol/port, expected result and actual result for every test.

| # | Source | Destination | Protocol / Port | Command | Expected | Actual result | Evidence |
|---|---|---|---|---|---|---|---|
| 1 | Student laptop (PowerShell) | `public-ec2` public IP | TCP 22 | `ssh -i <path-to>\new.pem ec2-user@<public-ip>` | Allowed | Logged in to `ip-10-20-2-117` | `06` |
| 2 | `public-ec2` | `example.com` | TCP 443 | `curl -I https://example.com` | Allowed | HTTP response headers received | `07` |
| 3 | `public-ec2` | `google.com` (resolver `10.20.0.2`) | DNS 53 | `nslookup google.com` | Allowed | Resolved to several addresses via `10.20.0.2#53` | `07` |
| 4 | `public-ec2` | `private-ec2` `10.20.11.81` | ICMP | `ping -c 4 10.20.11.81` | Allowed | 4 transmitted, 4 received, 0% loss, avg 0.507 ms | `09` |
| 5 | `public-ec2` | `private-ec2` `10.20.11.81` | TCP 22 | `ssh ec2-user@10.20.11.81` (agent forwarding) | Allowed | Logged in to `ip-10-20-11-81` | `09` |
| 6 | Student laptop (internet side) | `private-ec2` `10.20.11.81` | TCP 22 | `ssh -i <path-to>\new.pem -o ConnectTimeout=10 ec2-user@10.20.11.81` | Blocked | `Connection timed out` | `11` |
| 7 | `private-ec2` | `example.com` | TCP 443 | `curl -m 10 https://example.com` | Blocked | `Connection timed out after 10001 milliseconds` | `10` |

## Test setup

```powershell
# On the laptop (PowerShell)
Get-Service ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
ssh-add "<path-to>\new.pem"
ssh -A ec2-user@<public-ec2-public-ip>
```

```bash
# Inside public-ec2
curl -I https://example.com
nslookup google.com
ping -c 4 10.20.11.81
ssh ec2-user@10.20.11.81

# Inside private-ec2
curl -m 10 https://example.com
```

## Interpretation

- Tests 1 to 5 show the public subnet has working internet routing and DNS, and that the bastion path to the private subnet works.
- Test 6 shows the private instance cannot be reached from the internet: it has no public IP, its subnet has no route to the Internet Gateway, and `private-sg` only trusts `public-sg`.
- Test 7 shows the private instance has no outbound internet path, because `private-rt` has only the local route and no NAT Gateway exists.

## Not covered

- A live SSH attempt to `public-ec2` from a second (non-allowed) IP address was not performed. The restriction is evidenced by the `/32` SSH rule in `screenshots/05-public-sg-inbound-rules.png`.
