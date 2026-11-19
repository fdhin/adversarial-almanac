---
name: "scout2"
categories:
  - blackarch-scanner
version: "1182.5d86d46"
homepage: "http://isecpartners.github.io/Scout2/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "nccgroup/Scout2"
github-stars: 1716
github-pushed: "2018-11-28"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scout2

> AWS security-auditing tool that gathers account configuration over read-only API calls and reports risk areas in a local HTML report — the archived predecessor of Scout Suite.

## What it's for

AWS Scout2 collects an AWS account's configuration (IAM, EC2, S3, CloudTrail, RDS, Redshift, …) through the AWS API, highlights high-risk findings automatically, and writes a local HTML report it opens in your browser — a point-in-time, security-oriented view of the account without paging through the web console. It needs credentials with read-only access to the relevant services (the managed policies ReadOnlyAccess or SecurityAudit suffice), and because it only makes API calls, AWS does not require a penetration-testing request form for it. Development has stopped: the final release lives under nccgroup/Scout2 and the project migrated to Scout Suite ([[scoutsuite]]), which continues the lineage as a multi-cloud auditor — prefer that for new work.

## Typical use cases

### Audit the account behind ambient CLI credentials

One-command posture check on an already-configured machine.

```
scout2
```

Outcome: uses the ambient AWS credentials (AWS CLI/SDK configuration or an EC2 instance role), fetches the account configuration, then generates and opens the HTML report; upstream the console entry point is `Scout2` (pip package AWSScout2), which the packaged `scout2` wrapper runs under python2.

### Audit a named credential profile

Point the run at a non-default account.

```
scout2 --profile PROFILE_NAME
```

Outcome: reads that profile from `~/.aws/credentials` and audits the matching account; `Scout2 --help` lists the remaining options.

### Audit from an exported CSV key pair

Run with keys exported from IAM.

```
scout2 --csv-credentials credentials.csv
```

Outcome: reads the access key ID and secret from the CSV instead of a profile and produces the same report.

*No corpus evidence — derived from upstream documentation (nccgroup/Scout2 README.rst, archived upstream; invocation forms per upstream and the BlackArch wrapper).*
