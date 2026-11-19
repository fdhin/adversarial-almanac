---
name: "omnibus"
categories:
  - blackarch-recon
version: "129.88dbf5d"
homepage: "https://github.com/InQuest/omnibus"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "InQuest/omnibus"
github-stars: 357
github-pushed: "2024-05-20"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# omnibus

> Interactive OSINT investigation console that pivots across IPs, domains, emails, usernames, file hashes and Bitcoin addresses using pluggable data-source modules.

## What it's for

Omnibus (InQuest) is an interactive Python console for open-source intelligence collection, research and artifact management. You create "artifacts" (IPv4, FQDN, email, username, MD5/SHA hash, Bitcoin address), then run per-artifact-type modules against them — VirusTotal, Shodan, Censys, WHOIS, PassiveTotal, HaveIBeenPwned, OTX, ThreatCrowd and roughly two dozen more. Newly discovered indicators (e.g. IPs found by the DNS module) are automatically added as child artifacts so an investigation pivots and grows. Results are stored in a local MongoDB database, with the current session's artifact IDs cached in Redis. It is a beta-era Python 2.7 application: it needs working local MongoDB and Redis services, and API keys placed in `omnibus/etc/apikeys.json` (free-tier keys work for most modules). It collects and organizes only — it performs no scanning beyond an optional NMap module and no exploitation.

## Typical use cases

### Start a session and investigate a domain

Open the console, register the target, and run a single module against it (BlackArch ships the same entry point as the `omnibus` wrapper).

```
python omnibus-cli.py
omnibus> session
omnibus> new target.example.com
omnibus> virustotal target.example.com
```

Outcome: the domain is typed automatically (FQDN), the VirusTotal results print to the console, and the artifact's MongoDB document is updated with what the module returned; any IPs or hostnames the module discovers are created as linked child artifacts.

### Bulk-collect with a machine

Run every module available for an artifact's type in one command instead of invoking modules individually.

```
omnibus> machine target.example.com
```

Outcome: all FQDN-suitable modules execute in turn (WHOIS, DNS resolution, VirusTotal, Shodan, etc.), results stream to the console and are stored in the database — expect a large volume of child artifacts after a few minutes.

### Export an artifact dossier

Persist a pretty-printed JSON record of everything gathered on an artifact.

```
omnibus> cat target.example.com > target-report.json
omnibus> report target.example.com ./intel/example.json
```

Outcome: the first command redirects the artifact's beautified database document to a local file; the second writes the report through Omnibus's own reporting path (defaulting to `omnibus/reports/<artifact>_<timestamp>.json` when no path is given).

### Audit configured API keys

Check which service keys are actually stored before blaming modules for empty results.

```
omnibus> cat apikeys
```

Outcome: prints the current contents of the key store so you can spot missing entries; keys themselves are edited in `omnibus/etc/apikeys.json`, one JSON field per service.

> No corpus evidence — derived from upstream documentation.
