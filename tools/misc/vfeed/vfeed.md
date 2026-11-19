---
name: "vfeed"
categories:
  - blackarch-misc
version: "81.fad17ae"
homepage: "http://www.toolswatch.org/vfeed"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "toolswatch/vFeed"
github-stars: 950
github-pushed: "2021-05-28"
github-archived: true
github-issues: 9
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vfeed

> Python wrapper and CLI over the vFeed correlated vulnerability and threat-intelligence database.

## What it's for

vFeed cross-links a CVE identifier with what other standards and vendors know
about it — CWE, CPE, OVAL, CAPEC, CVSS, Exploit-DB and Metasploit references,
Nessus and Nmap plugin IDs, vendor advisories — and exposes those correlations
through a Python API and a small command line (the `vfeed` command, upstream
script `vfeedcli.py`). The SQLite database itself is distributed separately by
vFeed IO; the open-source repository provides the query layer, and lookups
return nothing until that database is in place. Use it to enrich a CVE found
during an assessment with exploitation references and severity context, or
embed the wrapper in your own tooling.

## Typical use cases

### List the available built-in functions

```
vfeed --list
```

Outcome: enumerates every lookup function the installed wrapper supports (get_cwe, get_cvss, get_msf, get_edb, get_nessus, ...), grouped by class — the vocabulary accepted by `-m` and `-e`.

### Pull one correlation for a CVE

```
vfeed -m get_cwe CVE-2021-44228
```

Outcome: prints the JSON object that function returns for the CVE (here its CWE classification), served entirely from the local database.

### Search without a CVE identifier

```
vfeed -s text "Apache Struts"
```

Outcome: runs a free-text search (also `cve`, `cpe`, `cwe`, `oval` as the first argument) over the local database and lists the matching CVE entries with their attributes — a starting point when only the product is known.

### Export the complete record for reporting

```
vfeed -e json_dump CVE-2021-44228
```

Outcome: dumps the full JSON content of every correlation held for the CVE in the local database, ready to attach to a finding or feed into downstream tooling.

*No corpus evidence — derived from upstream documentation.*
