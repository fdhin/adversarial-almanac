---
name: "scoutsuite"
categories:
  - blackarch-scanner
version: "5.14.0.r0.g7909f2f"
homepage: "https://github.com/nccgroup/ScoutSuite"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "nccgroup/ScoutSuite"
github-stars: 7870
github-pushed: "2025-09-23"
github-archived: false
github-issues: 298
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scoutsuite

> Multi-cloud security-auditing tool that maps an AWS, Azure or GCP account's attack surface into a single offline HTML report.

## What it's for

Scout Suite (successor of the AWS-only Scout2 — see [[scout2]]) assesses cloud security posture by pulling configuration through the provider APIs: AWS, Microsoft Azure and Google Cloud Platform are the mature providers, with Alibaba Cloud, Oracle OCI, DigitalOcean and provider-hosted Kubernetes clusters supported in alpha. It gathers everything up front and then works offline from the collected data, presenting findings and the full account configuration in an HTML report opened in your browser. Run it with credentials that can read the account (each provider's wiki page documents credential setup); it audits configuration and highlights risk, it does not exploit.

## Typical use cases

### Audit an AWS account

```
scout aws
```

Outcome: uses ambient AWS credentials (add `--profile PROFILE` to select one), collects the account configuration through the AWS APIs and generates a local HTML report that opens in the default browser, with findings grouped per service.

### Audit an Azure subscription

```
scout azure --cli
```

Outcome: authenticates through the local Azure CLI session, gathers the subscription's configuration and produces the same style of HTML report for offline review.

### Audit a Google Cloud project

```
scout gcp --user-account
```

Outcome: authenticates with the user-account flow for GCP, enumerates the project's configuration and writes the report for offline inspection.

*No corpus evidence — derived from upstream documentation (nccgroup/ScoutSuite README and wiki Home/Setup pages; console entry point `scout`, upstream also run as `python scout.py <provider>`).*
