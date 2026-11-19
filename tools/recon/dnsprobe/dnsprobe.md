---
name: "dnsprobe"
categories:
  - blackarch-recon
version: "56.7120008"
homepage: "https://github.com/projectdiscovery/dnsprobe"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-04-26"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/dnsprobe"
github-stars: 284
github-pushed: "2020-12-21"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsprobe

> Bulk DNS resolution of long host lists through user-supplied resolvers — ProjectDiscovery's query probe, predecessor of dnsx.

## What it's for

dnsprobe answers one need in enumeration pipelines: resolve thousands of hostnames quickly and print the ones that answer. It accepts hosts on stdin or from a file (`-l`), fans the queries out across a configurable number of threads and retries, and can push them through your own list of resolvers (`-s`) instead of the system default. It only resolves what it is given — it does not discover or bruteforce hostnames itself. The project is discontinued and archived upstream; its functionality lives on in dnsx, so new pipelines should generally start there.

## Typical use cases

### Resolve a discovered host list

Pipe hostnames in and get `host ip` lines back; full URLs are accepted and the domain extracted automatically.

```
subfinder -d example.com -silent | dnsprobe
```

Outcome: prints one line per resolved host (e.g. `www.example.com 192.0.2.10`); unresolved names are dropped, leaving a list ready to feed into a port scanner.

### Query a specific record type for a file of hosts

```
dnsprobe -l domains.txt -r CNAME
```

Outcome: prints each host in `domains.txt` followed by its CNAME target; `-r` also takes A, NS, SOA, PTR, MX, TXT and AAAA.

### Resolve through your own resolvers with tuning

```
dnsprobe -l hosts.txt -r A -s resolvers.txt -c 5 -t 500 -f json -o results.json
```

Outcome: retries each query up to 5 times (`-c`) across 500 concurrent workers (`-t`) using the resolvers listed one-per-line in `resolvers.txt`, and writes the answers as JSON to `results.json`.

*No corpus evidence — derived from upstream documentation.*
