---
name: "plecost"
categories:
  - blackarch-webapp
version: "104.4895e34"
homepage: "https://github.com/Plecost/plecost"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "Plecost/plecost"
github-stars: 388
github-pushed: "2026-08-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 217
gh-activity-checked: "2026-11-09"
---

# plecost

> Self-contained WordPress security scanner (core, plugins, themes) correlating findings against a local CVE database.

## What it's for

Auditing WordPress installations without external dependencies: it fingerprints the core version and enumerates plugins and themes (passively from page HTML, or actively with wordlists), then matches everything against a locally stored, daily-updated CVE database — no API keys, no third-party data sent per scan. Beyond version CVEs it checks a broad surface: user enumeration (REST API and author archives), XML-RPC exposure, security headers, TLS configuration, directory listing, wp-config/.env/.git leaks, webshell paths and e-commerce-specific modules (WooCommerce, WP eCommerce). Runs as a CLI, a Python library, or inside task queues. Version 4 is a from-scratch Python/asyncio rewrite by a new maintainer (repo transferred from `iniqua/plecost`; pre-v4 releases were the BSD-licensed "WordPress finger printer" by Iniqua) and is licensed PolyForm Noncommercial — commercial use needs a separate arrangement.

## Typical use cases

### First run: fetch the CVE database, then scan

Bootstrap the local database and run a default scan against a site.

```
plecost update-db
plecost scan https://target.example.com
```

Outcome: `update-db` populates the local SQLite CVE store; `scan` prints detected WordPress core/plugin/theme versions, a findings table with severity IDs (CVEs, header and TLS issues, user enumeration, XML-RPC) and a summary count by severity.

### Deep or aggressive scanning

Move from the fast default (top plugins/themes) to full coverage, or push concurrency on internal targets.

```
plecost scan --deep https://target.example.com
plecost scan --aggressive https://target.example.com
```

Outcome: `--deep` switches to the full wordlists (4,750+ plugin and 900+ theme slugs); `--aggressive` raises parallelism to 50 requests — keep it for targets you own, and prefer `--stealth` (random user agent, passive detection) on sensitive engagements.

### Authenticated scan through a proxy

Log in and route traffic through an intercepting proxy at the same time.

```
plecost scan --user admin --password 'Password1' --proxy http://127.0.0.1:8080 https://target.example.com
```

Outcome: the scan authenticates as the given WordPress user and sends every request through the proxy (HTTP or SOCKS5), letting Burp/ZAP observe or modify the traffic while findings that need authentication are included.

### Produce a machine-readable report

Save results for downstream tooling.

```
plecost scan --output report.json https://target.example.com
```

Outcome: writes the full findings set to `report.json`; `--quiet` on the console side trims output to HIGH/CRITICAL findings only. Use `--modules fingerprint,plugins,cves` / `--skip-modules` to scope which detection modules run.

*Provenance: no corpus evidence — derived from upstream documentation (v4 README + `plecost/cli.py`, verified 2026-11).*
