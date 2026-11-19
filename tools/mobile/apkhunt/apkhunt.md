---
name: "apkhunt"
categories:
  - blackarch-mobile
version: "2.0.0.r0.gd753172"
homepage: "https://github.com/Cyber-Buddy/apkhunt"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "Cyber-Buddy/apkhunt"
github-stars: 992
github-pushed: "2026-09-24"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# apkhunt

> Local web workbench that statically analyzes Android APKs and maps findings to the OWASP MASVS framework.

## What it's for

APKHunt is a self-hosted APK static-analysis workbench. You upload an APK (up to 200 MB by default) in the browser; it decompiles with JADX (Apktool as manifest/resource fallback), evaluates a rule set of ~39 checks across storage, crypto, authentication, network, platform and code surfaces (cleartext policy, expired certificate pins, unsafe WebView settings, FileProvider root paths, mutable PendingIntents, weak randomness, hardcoded keys, AES-ECB, ...), maps them to OWASP MASVS groups, builds a CycloneDX SBOM, enriches versioned dependencies through OSV, and saves the report with a coverage ledger, entry-point graph, STRIDE threat model, API-route inventory and TruffleHog secret-candidate snapshot. The project is explicit about limits: it does not verify MASVS compliance, and static matches are investigation targets, not proven vulnerabilities. The original Go CLI was retired into repository history — the current interface is the web workbench (on BlackArch the `apkhunt` launcher runs it via gunicorn on port 5005; upstream ships it as a docker compose stack bound to loopback).

## Typical use cases

### Scan an APK in the local workbench

Run a full MASVS-mapped static review of one APK from the browser interface.

```
apkhunt
```

Outcome: starts the workbench web server; open `http://localhost:5005` (the BlackArch launcher binds 0.0.0.0:5005), upload an APK, and the scan runs as a background job with a live status dock. When it completes, the saved report groups findings by MASVS area with severity and code references, alongside SBOM, dependency OSV states, coverage ledger, entry-point graph, threat model and secret review pages. Opening a saved report never re-runs decompilers or enrichment, and runtime proof pages (ADB activity-launch tests, scoped API route probes) are opt-in extras that require a connected device.

*No corpus evidence — derived from upstream documentation.*
