---
name: "prowler"
categories:
  - blackarch-defensive
version: "9309.669df91ac9"
homepage: "https://github.com/prowler-cloud/prowler"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "prowler-cloud/prowler"
github-stars: 15200
github-pushed: "2026-11-09"
github-archived: false
github-issues: 487
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2309
gh-activity-checked: "2026-11-09"
---

# prowler

> Open-source cloud security platform whose CLI audits accounts across AWS, Azure, GCP, Kubernetes and more.

## What it's for

Prowler runs thousands of ready-made configuration and security checks — 662 across 86 AWS services in the current release, with smaller sets for Azure, GCP, Kubernetes, GitHub, M365 and a dozen other providers — and maps findings to 50+ compliance frameworks (CIS, NIST, PCI-DSS, GDPR, ISO 27001, MITRE ATT&CK, and others) with severity scoring and remediation guidance. The current CLI is Python (`prowler <provider>`, installable from PyPI, Python 3.10–3.12); the pre-3.0 bash implementation no longer exists upstream. Note the historical naming: the upstream repo moved from the alfresco fork location to prowler-cloud/prowler, and older distro packages that launch a top-level `prowler` bash script predate that rewrite and cannot run current sources.

## Typical use cases

### Audit an AWS account

Run the full check catalogue against the account your credentials point to.

```
prowler aws
```

Outcome: Executes the AWS check set, printing per-check PASS/FAIL findings grouped by service and writing the result files for later review; the account used is whatever the standard AWS credential chain resolves to.

### Discover what can be scanned

Enumerate checks, services, or frameworks before committing to a scan.

```
prowler aws --list-checks
```

Outcome: Prints every AWS check identifier available in this version; `--list-services`, `--list-compliance`, and `--list-categories` enumerate the other dimensions the same way.

### Focus on the worst findings

Cut the report down to what needs triage first.

```
prowler aws --severity critical high
```

Outcome: The scan runs as usual but only critical- and high-severity findings are reported, giving the short list to work through before broader cleanup.

### Audit a non-AWS cloud

Apply the same workflow to another provider.

```
prowler azure
```

Outcome: Audits the Azure subscriptions your `az` login can reach, with findings in the same format as AWS scans; `gcp`, `kubernetes`, `github`, `m365` and the other supported providers work identically as the positional argument.

### Browse results in the local dashboard

Turn scan output into an interactive view.

```
prowler dashboard
```

Outcome: Starts the local dashboard web UI so completed scans can be explored and filtered in the browser instead of reading raw output files.

*No corpus evidence — derived from upstream documentation.*
