---
name: "user-scanner"
categories:
  - blackarch-social
version: "1.5.2"
homepage: "https://github.com/kaifcodec/user-scanner"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-20"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "kaifcodec/user-scanner"
github-stars: 5579
github-pushed: "2026-11-07"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1433
gh-activity-checked: "2026-11-09"
---

# user-scanner

> Modern username and email footprint scanner (2700+ sites) with cross-scan pivoting, infostealer-breach lookup and AI-agent integration.

## What it's for

User Scanner (kaifcodec) maps where a username or email address is registered across 2510+ username platforms and 210+ email-integrated sites, then goes a step further than simple checkers: it scrapes rich metadata (avatars, bios, follower counts, UIDs), and its cross-scan engine mines handles, profile links and exposed secondary email addresses out of the accounts it finds and automatically pivots onto them for multi-pass investigation. Extras that earn it a place in a workflow: `--hudson` checks the target against Hudson Rock infostealer malware logs, permutation patterns catch alias variants, reports export to PDF (with profile photos)/JSON/CSV, proxy rotation with reachability validation is built in, and an MCP server lets LLM agents (Claude Desktop, Cursor) drive scans. Use it for identity pivoting and footprint mapping in authorized investigations; it checks public registration signals only — no passwords or private data.

## Typical use cases

### Baseline a username or email

```
user-scanner -u user
user-scanner -e user@example.com
```

Outcome: parallel probes across all matching platform modules with a live category grid; found accounts are listed with whatever metadata the site exposes (avatar, bio, follower count). Add `--email-domains global` to also try the username as a local part across common email providers.

### Pivot from the first hit to everything connected

```
user-scanner -e user@example.com --cross-scan --cross-depth 2
```

Outcome: after the initial email scan, exposed handles, profile links and secondary emails are mined from the found profiles and each new vector is scanned in turn, two hops deep; `--cross-links verified` keeps only platform-verified links for a cleaner graph.

### Correlate with infostealer breach logs

```
user-scanner -u user --hudson
```

Outcome: the target is additionally checked against Hudson Rock's infostealer infection-log dataset, flagging if the identity appeared in malware-extracted credential dumps — a high-priority triage signal in an investigation.

### Scope a scan to categories or single modules

```
user-scanner -u user -c dev
user-scanner -u user -m github,instagram
```

Outcome: only the `dev` category (or the named modules) is probed instead of all 2500+ sites; `user-scanner -lu` and `user-scanner -le` print the full username/email category-module grids to pick from.

### Batch targets through proxies and export

```
user-scanner -uf usernames.txt -f pdf -o report.pdf -P proxies.txt --validate-proxies
```

Outcome: every username in the file is scanned (one per line; `-ef` does the same for emails), the reachability of every proxy is verified before use, and results land in a PDF report (swap `-f json`/`-f csv` for pipelines). `user-scanner-mcp` starts the same engine as a Model Context Protocol server for AI agents.

*No corpus evidence — derived from upstream documentation.*
