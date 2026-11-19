---
name: "reconness"
categories:
  - ai
version: "git (2026-10-12)"
homepage: "https://github.com/reconness/reconness"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "reconness/reconness"
github-stars: 326
github-pushed: "2026-10-12"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 25
---

# reconness

> ReconNess — a self-hosted web platform for continuous recon: chain your existing recon tools as "Agents", run them on schedules or events, and keep every result organized per target.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Replacing the folder-of-scripts chaos that bug bounty recon becomes. ReconNess is a web application where each recon tool you already use is registered as an Agent with its command and configuration; agents are chained into pipelines that fire on a schedule or on events (a new subdomain found, a scan finishing), and all output is stored against the target tree — so the potentially-vulnerable surface stays visible without bash archaeology. You bring the tools (it orchestrates, it doesn't replace nmap/sublist3r/etc.), and the learning effort goes into exploitation rather than folder management. Deployment is via its documented quick start (docs.reconness.com); upstream activity has stalled (0 commits in the last year), so pin your instance and treat upgrades carefully.

## Typical use cases

### Stand up the platform

Follow the upstream Quick Start (docs.reconness.com/getting-started/quick-start).

Outcome: a running ReconNess web instance where you register targets, and per-target notes and results accumulate — the base everything else hangs off.

### Turn a tool into a scheduled agent

Register e.g. sublist3r or amass as an Agent with its command line inside the UI, attach it to a target, and set a schedule.

Outcome: on each trigger the agent runs against the target and its parsed results are stored with that target automatically — new findings feed notifications and downstream agents in the chain, giving you continuous recon rather than one-off scan nights.
