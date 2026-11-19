---
name: "pingcastle"
categories:
  - blackarch-windows
version: "3.4.1.38"
homepage: "https://www.pingcastle.com"
packaged-by: [blackarch]
blackarch-builddate: "2025-08-10"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "netwrix/pingcastle"
github-stars: 2994
github-pushed: "2026-11-05"
github-archived: false
github-issues: 63
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# pingcastle

> PingCastle — the reference Active Directory risk-assessment tool: one command scores a domain's security posture and produces an HTML report in minutes.

## What it's for

PingCastle (created by Vincent Le Toux, now published by Netwrix) audits Active Directory security with an explicitly pragmatic methodology — "get 80% of the security issues in 20% of the time" — instead of exhaustive enumeration. A healthcheck run collects the domain's configuration (users, groups, trusts, delegation, GPO indicators, replication metadata) and scores it against a mature risk-assessment framework, producing a self-contained HTML report with a score, findings by risk level, and remediation guidance. Beyond the domain healthcheck it can score Entra ID tenants, aggregate many reports into one consolidated view, build a map (cartography) of all interconnected/trusted domains, run targeted security checks on workstations (smb, spooler, nullsession, zerologon-class scanners), and export users or computers. It runs interactively by default (a numbered menu) and every menu action has a command-line switch, so it fits both ad-hoc inspection and scheduled/CI audits. The open-source edition is licensed under the Non-Profit OSL 3.0 (free internal use, including by for-profit companies; commercial licensing exists for paid services built on it). It needs valid domain credentials and network reachability to a domain controller, but no agent and no installation on the DC.

## Typical use cases

### Score a domain's risk (the core healthcheck)

The canonical run against the current domain.

```
PingCastle.exe --healthcheck
```

Outcome: authenticates with the current user's credentials, collects the domain's state and writes an HTML report file (plus XML data) in the working directory; the report opens with an overall score (0–100, lower is better) and findings grouped by risk level with remediation hints. Interactive mode (run with no arguments and pick `1-healthcheck`) does the same through the menu.

### Audit a specific remote domain or forest

Target a domain other than the logged-on one, optionally following trust links.

```
PingCastle.exe --healthcheck --server corp.example.com
```

Outcome: `--server` directs the collection at that domain (e.g. through an existing trust); combine with `--explore-trust` to iterate healthchecks over trusted domains discovered along the way.

### Run a targeted workstation security check

Instead of the full domain report, run one focused scanner against the domain's machines.

```
PingCastle.exe --scanner nullsession --server corp.example.com
```

Outcome: runs the `nullsession` scanner across the domain's computers and prints which hosts expose an unauthenticated SMB null session; other scanner names include `spooler`, `zerologon`, `antivirus` and `oxidbindings` — an invalid name is rejected with the list of available scanners.

### Map and consolidate a multi-domain estate

Two report-level operations for large organisations.

```
PingCastle.exe --carto
PingCastle.exe --hc-conso
```

Outcome: `--carto` builds a map of all interconnected domains (lighter than running healthchecks everywhere — it combines trust data only), and `--hc-conso` merges previously generated healthcheck reports into a single consolidated report covering the whole audited scope.

*No corpus evidence — derived from upstream documentation and the current source's switch table.*
