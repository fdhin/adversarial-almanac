---
name: "cloudfox"
categories:
  - recon
homepage: "https://github.com/BishopFox/cloudfox"
origin: "cloudpentestcheatsheets"
recipe: "upstream"
packaged-by: []
build-system: go
license: MIT
meta-checked: "2026-11-13"
github-repo: "BishopFox/cloudfox"
github-match: "exact"
github-stars: 2620
gh-checked: "2026-11-13"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# cloudfox

> BishopFox's situational-awareness CLI for cloud pentests — maps an unfamiliar AWS, Azure or GCP account and surfaces the attack paths worth chasing.

## What it's for

CloudFox is an open-source command-line tool built to help penetration testers and other offensive security professionals find exploitable attack paths in cloud infrastructure. It enumerates an account read-only and answers pentest-shaped questions: which workloads have admin permissions, what secrets lurk in EC2 userdata or service environment variables, which role trusts are overly permissive or cross-account, which endpoints are attackable from an external or internal starting point, and which filesystems might be mountable from inside the VPC. It supports 34 AWS commands, 4 Azure commands and 60 GCP commands under a `cloudfox <provider> <command>` grammar. It runs white-box with a limited read-only principal (recommended: AWS `SecurityAudit` plus the upstream custom policy), or black-box with found credentials — failed checks fail silently, so anything it returns proves the creds can read it. It deliberately does not exploit anything, and it is not a compliance/baseline scanner — no alerts or findings, just the data you need to validate attack paths by hand.

## Typical use cases

### Run the full AWS sweep on a profile

First contact with an unfamiliar account.

```
cloudfox aws --profile user all-checks
```

Outcome: every AWS check runs with sane defaults (inventory, principals, permissions, role trusts, endpoints, secrets, workloads, ...) printing tables and writing loot files under the cloudfox output directory; start here, then drill into individual commands with their non-default options.

### Check what a principal can actually do

Skip the console's policy-expansion clicking.

```
cloudfox aws --profile user permissions
```

Outcome: a table of the IAM permissions of all users and roles in the account — grep it to answer "what can this principal do"; `cloudfox aws --profile user iam-simulator` additionally evaluates a principal's effective actions through the IAM policy simulator, though only `pmapper`-backed analysis considers transitive privesc paths.

### Harvest secrets from workload configuration

Secrets leaking through managed-service config.

```
cloudfox aws --profile user env-vars
```

Outcome: environment variables from App Runner, ECS, Lambda, Lightsail containers and SageMaker are printed — follow up on anything sensitive with `cloudfox iam-simulator` or pmapper to see who else can reach it; `cloudfox aws --profile user instances --userdata` pulls EC2 userdata for the same purpose.

### Hunt permissive trust relationships

Cross-account and service-trust attack paths.

```
cloudfox aws --profile user role-trusts
```

Outcome: trust policies of every IAM role, ready to eyeball for wildcard principals, cross-account assumptions or unexpected service trusts; `cloudfox aws --profile user resource-trusts --include-kms` does the same for resource policies (KMS key policies are excluded unless the flag is passed).

### Map the external attack surface

Feed a scanner the right targets.

```
cloudfox aws --profile user endpoints
```

Outcome: endpoints enumerated across services (instances, load balancers, databases, APIs, ...), split into externally and internally reachable sets — scan both positions for unauthenticated or misconfigured services; the loot file carries per-target curl/nmap-ready output.

### Sweep many stored profiles at once

Multiple accounts handed over in one engagement.

```
cloudfox aws -l profiles.txt all-checks
```

Outcome: the sweep runs against every AWS profile named in the file (one per line); `-a` instead targets every profile stored on the machine.

### Who can privesc to admin

Graph-backed escalation analysis.

```
cloudfox aws --profile user pmapper
```

Outcome: if PMapper graph data exists locally (run `pmapper graph create` first), cloudfox loads it and reports which principals can reach admin; `cloudfox aws --profile user cape` then enumerates cross-account privilege-escalation paths from the same graph.

### Orient in Azure or GCP

The same idea on the other clouds.

```
cloudfox gcp whoami
```

Outcome: prints the identity context of the authenticated GCP session (or, with `cloudfox azure whoami`, tenant/subscription/resource-group IDs for Azure) — the IDs the rest of that provider's commands need; GCP alone adds 60 commands from `iam` and `privesc` to `foxmapper` graph analysis.

No corpus evidence — derived from upstream documentation.
