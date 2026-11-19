---
name: "udork"
categories:
  - blackarch-recon
version: "102.1a0aab0"
homepage: "https://github.com/m3n0sd0n4ld/uDork"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "m3n0sd0n4ld/uDork"
github-stars: 867
github-pushed: "2022-06-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# udork

> Google-dorking menu (Bash) that mined files, directories, IoT devices and web-app versions via Facebook-session-authenticated Google scraping — non-functional since June 2022.

## What it's for

uDork (by M3n0sD0n4ld) automated Google Hacking: it wrapped predefined dorks and the Exploit-DB Google Hacking Database to list exposed files by extension, find directories containing words like "password", locate IoT devices and fingerprint web application versions for a target domain — all by scraping Google through an authenticated Facebook/Messenger web session (the `c_user` and `xs` cookies pasted into the script), which is how it sidestepped unauthenticated Google rate limits. It never attacked servers directly; it only issued search queries. Important status fact: the author announced on 2022-06-11 that Facebook withdrew the service the tool relied on, so uDork no longer works; the author's successor tool is GooFuzz. Keep uDork as a historical dork-catalog reference — the categories it exposed (per-extension file search, directory word search, dork mass-runs) remain useful as a checklist to reproduce with any working Google/Bing search interface.

## Typical use cases

### Open the dork menu

Display the tool's category menu (historical reference).

```
udork -h
```

Outcome: prints the interactive menu of dork categories — file-type enumeration, directory keyword search, IoT/web-app detection and massive dork runs — which serves as a compact checklist of Google-Hunting categories even though the request path behind them is dead (Facebook withdrew the backing service in June 2022).

*No corpus evidence — derived from upstream documentation. Tool announced non-functional by its author; examples retained for reference only.*
