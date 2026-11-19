---
name: "forager"
categories:
  - blackarch-recon
version: "v2.0.3.r3.g7439b0a"
homepage: "https://github.com/byt3smith/Forager"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "byt3smith/Forager"
github-stars: 176
github-pushed: "2018-01-23"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# forager

> Threat-intelligence feed manager that pulls 15 preconfigured feeds and report files into plain text files, then hunts them for IPs and other indicators.

## What it's for

Forager (byt3smith, Python 3) is for small threat-intel workflows that do not want a database: it fetches indicator data from ~15 preconfigured threat feeds (URL-based, with modular fetchers), extracts domains, IPv4 addresses, MD5/SHA1/SHA256 hashes and YARA rules, and stores everything as simple text files in an intel directory. From that local store you can hunt for a single IP or a whole IOC file, and generate JSON feeds consumable by Carbon Black, including a built-in HTTP feed server. It can also extract indicators from threat reports you feed it (TXT, PDF, XLS/XLSX — PDF extraction is prone to false positives from URL references). It gathers and organizes intelligence; it does no scanning or exploitation.

## Typical use cases

### Pick a single feed to update

Interactively choose which feed to refresh.

```
forager --feeds list
```

Outcome: prints all configured feed modules and prompts for one to update — useful when only one feed matters instead of pulling all 15.

### Refresh every feed

Pull the full intel set.

```
forager --feeds update
```

Outcome: runs every feed module in Forager and writes the fetched indicators into the local intel directory as text files, ready for hunting or feed generation.

### Check a single IP against collected intel

See whether an address you are investigating is a known indicator.

```
forager --hunt -s 198.51.100.10
```

Outcome: searches the intel directory for the given IPv4 address and prints every feed result that contains it.

### Hunt a whole IOC list

Screen a file of indicators at once.

```
forager --hunt -f iocs.txt
```

Outcome: checks each indicator in `iocs.txt` against the intel directory and reports matches — a bulk triage of an incident's observables against stored threat data.

### Extract indicators from a threat report

Turn a downloaded PDF or spreadsheet into queryable intel.

```
forager --extract threat-report.pdf
```

Outcome: parses the file (TXT, PDF, XLS/XLSX supported) and places the extracted IP addresses, domains, hashes and YARA rules into the intel directory for future hunts.

### Generate a Carbon Black JSON feed

Package the stored IOCs for a Carbon Black server.

```
forager --cbgen
```

Outcome: writes JSON feeds of all indicators in the intel directory; the first run prompts interactively for feed metadata.

### Serve the generated feed to Carbon Black

Host the feed so Carbon Black can ingest it automatically.

```
forager --srv
```

Outcome: starts the built-in HTTP feed server that serves the JSON feeds produced by `--cbgen`, letting a Carbon Black server pull them on schedule.

*No corpus evidence — derived from upstream documentation.*
