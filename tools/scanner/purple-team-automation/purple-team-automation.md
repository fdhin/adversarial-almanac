---
name: "purple-team-automation"
categories:
  - scanner
version: "git (2026-09-25)"
homepage: "https://github.com/joshuagodwin7929/purple-team-automation"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "joshuagodwin7929/purple-team-automation"
github-stars: 59
github-pushed: "2026-09-25"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 46
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# purple-team-automation

> A purple-team validation bundle: custom MITRE Caldera abilities and a chained AD adversary profile that execute credential-access techniques against a lab domain, with Sigma detections confirmed in Kibana and coverage mapped onto an ATT&CK Navigator heatmap.

## What it's for

This repository is not a program but an automation bundle for detection validation. It ships custom Caldera ability YAMLs — Kerberoasting, AS-REP roasting, password spraying and DCSync, built on Impacket scripts and Kerbrute because the stock abilities are Windows/.NET-only — plus a chained adversary profile that orders them the way an opportunistic internal attacker would (spray → Kerberoast → AS-REP roast → DCSync). Validation evidence lives in per-technique Kibana notes, including a documented known gap for LLMNR/NBT-NS poisoning, and an ATT&CK Navigator layer JSON visualizes the coverage. Use it by importing the abilities and adversary profile into your own Caldera server against your own lab domain, then comparing what fired in your SIEM; the repo also contains the full report of the run. There is no CLI to document — the deliverables are YAMLs, a heatmap JSON, and a report.

*No corpus evidence — derived from upstream documentation (repo README). Description-only: the repository ships Caldera/Navigator artifacts and a report rather than a runnable command-line interface, so no example commands are given.*
