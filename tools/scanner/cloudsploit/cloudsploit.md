---
name: "cloudsploit"
categories:
  - blackarch-scanner
version: "6744.cf9cd42d1"
homepage: "https://github.com/aquasecurity/cloudsploit"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: node
license: GPL3
meta-checked: "2026-11-09"
github-repo: "aquasecurity/cloudsploit"
github-stars: 3790
github-pushed: "2026-09-22"
github-archived: false
github-issues: 212
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 121
gh-activity-checked: "2026-11-09"
---

# cloudsploit

> Cloud misconfiguration scanner (CloudSploit by Aqua) for AWS, Azure, GCP, Oracle OCI and GitHub accounts.

## What it's for

CloudSploit (maintained by Aqua Security as aquasecurity/cloudsploit; originally cloudsploit/scans) detects security risks in cloud infrastructure accounts: open security groups, over-privileged IAM keys, unencrypted storage, disabled logging and hundreds of similar misconfigurations. It works in two phases — collecting metadata through the cloud provider APIs, then evaluating plugin checks against the collected data — and needs only read-only credentials (for AWS it can use the default SDK credential chain; other providers use a JSON credential file). Results can be filtered against compliance programs (HIPAA, PCI, CIS), emitted as CSV/JSON/JUnit output, and non-passing findings can fail a CI run via an exit code. A hosted commercial version exists (Aqua Wave); this note covers the open-source CLI.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Run a full account scan

Assess every plugin against the configured credentials.

```
cloudsploit --config config.js
```

Outcome: collects account metadata via the cloud APIs and prints a table of every check with status (PASS/WARN/FAIL), affected resource IDs and remediation guidance; the wrapper runs the repository's index.js entry point directly.

### Filter to a compliance program

Report only the checks mapped to a program.

```
cloudsploit --config config.js --compliance pci
```

Outcome: output is restricted to findings applicable to the selected program (choices: hipaa, cis, cis1, cis2, pci).

### CI-friendly run

Fail builds on findings and archive the results.

```
cloudsploit --config config.js --csv results.csv --ignore-ok --exit-code
```

Outcome: writes results.csv, omits passing checks from the console output, and exits with a non-zero status when any non-passing result is found — suitable as a pipeline gate.

### Run a single plugin

Debug or re-run one specific check.

```
cloudsploit --config config.js --plugin acmValidation
```

Outcome: only the named plugin executes (plugin names come from the repository's exports.js file), printing that check's result for every affected resource.
