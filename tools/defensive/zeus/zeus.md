---
name: "zeus"
categories:
  - blackarch-defensive
version: "111.97db152"
homepage: "https://github.com/DenizParlak/Zeus"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "DenizParlak/Zeus"
github-stars: 716
github-pushed: "2020-02-04"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zeus

> Menu-driven bash auditor that benchmarks an AWS account against CIS-derived rules and can re-harden it on request.

## What it's for

Zeus is an AWS security-auditing shell script built on aws-cli: it walks an account through an interactive menu and checks it against a packaged set of CIS AWS Benchmark-derived controls — IAM hygiene (root account use, MFA coverage, key rotation, password policy), logging (CloudTrail in all regions, log validation, KMS encryption), networking (security groups open on ports 22/3389, VPC flow logging), and CloudWatch alarm coverage — then, only when you ask, changes the offending settings to the recommended values. Checks are scoped by profiles you define, so an audit can target a subset of services. It is purely interactive (no documented non-interactive flags) and reads credentials from the standard aws-cli profile. Despite the name, it has nothing to do with the Zeus banking trojan.

## Typical use cases

### Audit an AWS account interactively

```
aws configure
zeus
```

Outcome: after verifying that aws-cli is installed and a profile is configured, zeus presents its module menu (IAM, Logging, Networking, Monitoring); select a profile to scope the run and each control's result is printed to the terminal, with the option to apply the CIS-recommended hardening to findings.

*No corpus evidence — derived from upstream documentation.*
