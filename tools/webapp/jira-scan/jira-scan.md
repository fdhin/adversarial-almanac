---
name: "jira-scan"
categories:
  - blackarch-webapp
version: "7.447d0ec"
homepage: "https://github.com/bcoles/jira_scan"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "bcoles/jira_scan"
github-stars: 118
github-pushed: "2023-01-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jira-scan

> Ruby scanner and library that fingerprints Atlassian Jira instances and enumerates their exposed information.

## What it's for

jira_scan (by bcoles, shipped as the `jira-scan` binary from the `jira_scan` gem) is a lightweight remote check for Atlassian Jira. It confirms whether a URL really is Jira (via the dashboard or login page), recovers the Jira version, and tests a battery of known information-disclosure points: open user registration, Service Desk registration, the user-picker endpoints, META-INF exposure, gadget lists, dashboards, filters, projects and user lists. It is a detection/audit tool — it runs the checks and prints findings; it does not exploit anything. The same checks are available as a Ruby library for embedding into your own tooling.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Fingerprint and audit a Jira instance

Point the scanner at a suspected Jira host.

```
jira-scan https://jira.example.com
```

Outcome: prints whether the target is Jira, its detected version, and which of the checked endpoints (user registration, user pickers, META-INF, REST metadata) are exposed — a quick external attack-surface summary.

### Scan with verbose output and relaxed TLS checks

Handle self-signed certificates and see each individual check.

```
jira-scan -u https://jira.example.com -i -v
```

Outcome: certificate validation is skipped (`-i`) so internal hosts with self-signed TLS still scan, and verbose mode (`-v`) logs every check as it runs rather than only the findings.

### Use the checks from Ruby

Call single detectors programmatically.

```
ruby -r jira_scan -e 'puts JiraScan::getVersionFromDashboard("https://jira.example.com")'
```

Outcome: loads the gem's library and prints the Jira version recovered from the dashboard page; the same module exposes `detectJiraLogin`, `userRegistration`, `restUserPicker`, `getUsersFromUserPickerBrowser` and the other checks as one-call methods.
