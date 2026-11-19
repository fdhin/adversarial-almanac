---
name: "fiercephish"
categories:
  - spoof
version: "git (2024-01-09)"
homepage: "https://github.com/raikia/fiercephish"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "raikia/fiercephish"
github-stars: 1438
github-pushed: "2024-01-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 49
---

# fiercephish

> Raikia's FiercePhish: a self-hosted web application for running phishing engagements end to end — campaign tracking, scheduled email sending, and results collection behind one interface.

*No corpus evidence — derived from upstream documentation. Description-only: FiercePhish is a web framework whose only command surface is its installer/updater; day-to-day use happens in the browser UI, so no example commands belong on this page (installation lives in [[install]].*

## What it's for

Managing phishing engagements as projects rather than one-off scripts. Where a tool like GoPhish focuses on mailing infrastructure, FiercePhish aims at the full engagement lifecycle: separate campaigns tracked concurrently, scheduled and batched sending of emails, SMTP and IMAP services for sending and reading replies (the automated installer sets up both on the host), and a planned roadmap covering website spoofing, click tracking and notification options. It is deployed as a server: install on a fresh Ubuntu box (16.04–20.04 supported by the installer), point a purchased sending domain's DNS at it per the wiki's instructions, then operate everything through the web interface. It was renamed from FirePhish in early 2017 (older screenshots still show the previous branding) and its last upstream activity was January 2024 — a period when its feature set was well behind current alternatives, so treat it as a legacy option. All documentation beyond the README lives in the GitHub wiki (installation, configuration file reference, DNS setup), not in `--help` output.
