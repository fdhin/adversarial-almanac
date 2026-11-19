---
name: "nsia"
categories:
  - scanner
version: "1.0.6"
homepage: "http://threatfactor.com/Products/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: custom:${pkgname}
meta-checked: "2026-11-09"
github-repo: "LukeMurphey/nsia"
github-stars: 1
github-pushed: "2020-03-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nsia

> NSIA (Network System Integrity Analysis): a self-contained Java "website IDS" that continuously monitors sites for defacement, errors, information leaks and policy violations.

## What it's for

NSIA is a website integrity monitor from the ThreatFactor project: a Java server application that repeatedly fetches configured sites and analyzes the content with ThreatScript (deep content change) and ThreatPattern (known-bad: attacks, offensive language, leaked information) signatures, flagging defacements, outages, compliance violations and information disclosure. Everything is bundled — integrated database (no DBA needed, backups/defragmentation handled), web dashboard with multi-user access control, scan-policy management — so deployment is "point it at your sites" rather than assembly. It is a monitoring/defensive product, not an attack scanner, and it is Java-webapp-shaped rather than CLI-shaped: the operational surface is its web UI, with a couple of launcher scripts. Upstream has been dormant since 2020 and the vendor site is defunct; the GitHub repo (LukeMurphey/nsia) is the surviving source.

## Typical use cases

### Install the server

```
cd nsia/bin && ./install.sh
```

Outcome: the interactive installer checks for a Java runtime, sets up the working directories and configuration, and prompts for the initial administrative account — run once from the extracted distribution before first start.

### Run the scanner service

```
java -jar nsia.jar
```

Outcome: starts the NSIA server (this is exactly what the bundled `bin/run.sh` does); the web dashboard becomes available for logging in, registering the sites to monitor, defining scan policies and reviewing rule status and scan reports. Windows hosts alternatively run the bundled `ThreatFactor NSIA Service.exe`.

*No corpus evidence — derived from upstream source (README and bin/install.sh, bin/run.sh of LukeMurphey/nsia).*
