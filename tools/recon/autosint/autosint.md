---
name: "autosint"
categories:
  - blackarch-recon
version: "236.25d292c"
homepage: "https://github.com/bharshbarger/AutOSINT"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "bharshbarger/AutOSINT"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# autosint

> Run the standard OSINT lookups for a client and domain in one pass by chaining existing tools and search dorks.

## What it's for

AutOSINT automates the repetitive first-hour OSINT tasks for an engagement: given a client name and domain (plus your own search dorks), it runs whois and DNS lookups, calls theHarvester for emails/hosts, pyFoca for metadata, executes the supplied Google dorks, and checks any hashcat-style pot files (`user:hash` / `hash:plain`) you point it at for already-cracked credentials belonging to the target. It is a Python 2 orchestrator — it expects `whois`, `host`, `git`, theHarvester and pyFoca in your PATH — not a scanner itself. The original upstream repository (bharshbarger/AutOSINT) has been deleted; behavior is documented from the archived project snapshot, and the BlackArch package keeps the `autosint` wrapper (it launches `AutOSINT.py` under Python 2).

## Typical use cases

### Run the full OSINT pass for a client

```
autosint -c "Example Corp" -d example.com "example corp employees"
```

Outcome: executes the whois, DNS, theHarvester, pyFoca and Google-dork lookups for the named client and domain and aggregates the findings on the terminal — a single command for the manual lookups you would otherwise run tool-by-tool.

### Show everything each source returns

```
autosint -v -c "Example Corp" -d example.com "example corp employees"
```

Outcome: verbose mode dumps each tool's raw output to the screen instead of only the summarized findings, letting you verify individual hits before acting on them.

*No corpus evidence — derived from upstream documentation (archived project snapshot; upstream repository deleted).*
