---
name: "Obscura"
categories:
  - recon
version: "git (2026-07-08)"
homepage: "https://github.com/security-life-org/Obscura"
origin: "kitploit"
recipe: "upstream"
packaged-by: [aur, nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "security-life-org/Obscura"
github-stars: 2
github-pushed: "2026-07-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# Obscura

> Self-contained attack-surface management and recon platform: one static Go binary running 50 correlated recon modules behind a local web UI and REST API.

## What it's for

Obscura Scan is a recon/OSINT platform that ships as a single static Go binary — no Python environment, external database, or system libraries. Point it at a domain, URL, or IP and it runs dozens of modules concurrently (DNS, TLS/certificates, subdomain discovery, takeover detection, JS endpoint and secret extraction, typosquatting, JARM, cipher auditing, cloud-bucket hunting, email-auth posture, and more), correlates and de-duplicates the results, and scores them into a 0-100 risk rating with per-finding confidence. Everything is served through a web UI with live progress and a REST API. Most modules need no third-party API keys; key-gated modules skip with a stated reason. Outbound requests go through an SSRF-hardened dialer that blocks private/loopback/metadata ranges by default. It is recon and monitoring only — it does not exploit anything it finds.

## Typical use cases

### Start the scanner and run a first scan

Bring up the platform and scan a target from the dashboard.

```
./obscura
```

Outcome: the server listens on 127.0.0.1:8080; opening it in a browser shows the dashboard where you enter a target such as `example.com`, pick a profile (Quick / Full / Bug Bounty / Compliance) and mode (Defensive or Semi-offensive), and start the scan — progress streams live and finishes in a report with a risk gauge, severity-sorted findings, and per-module tables.

### Run on a custom port

```
OBSCURA_PORT=9090 ./obscura
```

Outcome: the web UI and API listen on port 9090 instead of 8080; every other behavior is unchanged. All host/port/database configuration is environment-variable driven this way.

### Scan internal ranges (authorized testing only)

Lift the default-deny SSRF guard for private/loopback targets.

```
./obscura --allow-internal
```

Outcome: scans may target private ranges such as RFC1918 space and loopback, enabling internal attack-surface assessment on networks you are authorized to test; without the flag such targets are refused.

### Export a finished report

Pull a scan's output in a report or SIEM format via the API.

```
curl -o report.pdf http://127.0.0.1:8080/export/pdf/1
```

Outcome: writes the PDF report for scan #1 to `report.pdf`; JSON, CSV, STIX 2.1, Splunk-CIM, QRadar-LEEF, Elastic-ECS and DOCX exports use the same `/export/<format>/<scan-id>` pattern, so results can go straight into a ticket or a SIEM.

### Create an API key for automation

Mint a bearer token so scripts can drive scans and fetch results.

```
./obscura --mint-key ci --mint-role admin
```

Outcome: prints a newly created API key named "ci" once and exits; requests bearing it as a Bearer token can use the REST API with admin rights, enabling CI-driven scanning without leaving the API open.

*No corpus evidence — derived from upstream documentation.*
