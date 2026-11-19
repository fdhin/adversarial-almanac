---
name: "seekr"
categories:
  - blackarch-recon
version: "0.4.0"
homepage: "https://github.com/seekr-osint/seekr"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "seekr-osint/seekr"
github-stars: 890
github-pushed: "2026-06-16"
github-archived: false
github-issues: 65
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# seekr

> Self-hosted OSINT workstation — target database, integrated lookups and a web UI on port 8569.

## What it's for

Seekr bundles common OSINT lookups behind a local web interface backed by a Go/BadgerDB service: it maintains per-target records with predefined fields, builds "account cards" for people, integrates tools such as phoneinfoga, resolves GitHub accounts to email addresses, and recommends web sources based on the data you already have — no API keys are required for its core features. It is beta software aimed at investigators who want note-taking and lookups in one place rather than a scriptable CLI; interaction happens in the browser, not the terminal. It enriches and organizes data you feed it — it is not a network scanner.

## Typical use cases

### Launch the workstation

```
seekr
```

Outcome: starts the backend and web interface; open http://localhost:8569/web/ to create targets, run lookups and manage the database.

### Run it via Docker

```
docker run -p 8569:8569 ghcr.io/seekr-osint/seekr:latest
```

Outcome: the same interface in a container, reachable on the published port without a local Go toolchain.

*No corpus evidence — derived from upstream documentation.*
