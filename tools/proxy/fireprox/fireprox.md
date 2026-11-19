---
name: "fireprox"
categories:
  - blackarch-proxy
version: "51.a6e1300"
homepage: "https://github.com/ustayready/fireprox"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "ustayready/fireprox"
github-stars: 2291
github-pushed: "2023-04-03"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fireprox

> AWS API Gateway management tool that spins up HTTP pass-through proxies on demand, so every request to your target leaves from a different AWS egress IP — cheap source-IP rotation for web testing.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Hiding or continually rotating the source IP address of web calls without renting fleets of VPS or buying proxy plans: fireprox provisions an AWS API Gateway endpoint that passes through to a destination URL, and each request through that gateway egresses from a different AWS address. All HTTP methods, parameters and URIs pass through unchanged, regions are configurable per proxy, and a request header trick (`X-My-X-Forwarded-For`) controls the `X-Forwarded-For` value the destination sees. It needs AWS credentials (inline keys, session token, or an existing profile) and costs API Gateway money while endpoints exist — delete what you are done with. It is an HTTP pass-through rotator only: no SOCKS, no non-HTTP protocols, and the upstream README warns that use against systems you do not own can violate the AWS Acceptable Use Policy.

## Typical use cases

### Create a rotating proxy for a target

```
fireprox --profile_name lab --region us-east-1 --command create --url https://target.example.com
```

Outcome: provisions an API Gateway stage in the chosen region pointing at the destination and prints the proxy URL (an `execute-api.<region>.amazonaws.com` address); point your tooling at that URL instead of the target and each request arrives from a fresh AWS IP with the destination's response passed straight back.

### List, repoint and tear down proxies

```
fireprox --profile_name lab --region us-east-1 --command list
fireprox --profile_name lab --region us-east-1 --command update --api_id a1b2c3d4e5 --url https://other.example.com
fireprox --profile_name lab --region us-east-1 --command delete --api_id a1b2c3d4e5
```

Outcome: `list` prints every existing proxy with its API ID, endpoint URL and destination; `update` repoints an existing gateway at a new URL without recreating it; `delete` removes the gateway for an API ID so it stops costing money.
