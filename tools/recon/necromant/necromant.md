---
name: "necromant"
categories:
  - blackarch-recon
version: "4.53930c2"
homepage: "https://github.com/PentesterES/Necromant"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "PentesterES/Necromant"
github-stars: 4
github-pushed: "2023-02-10"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# necromant

> Dead virtual host searcher: matches a list of candidate hostnames against web servers by fingerprinting their response to a forged Host header.

## What it's for

Necromant (Jose Selvi, PentesterES, v0.1 2011) takes two input files — a list of candidate vhost names and a list of web server URLs (`http://IP` or `http://IP:port`) — and finds which hostnames are actually served by which IP. For every server it first requests `/` with a deliberately nonexistent `Host:` header to build a baseline fingerprint (MD5 over status, redirect target, and normalized body), then repeats the request with each candidate hostname; a different fingerprint means that vhost lives on that server even though DNS no longer points there ("dead" or migrated virtual hosts). This is Host-header enumeration, not DNS brute forcing. It is a Python 2 script whose upstream is archived and marked deprecated by the author, so expect to port it (htplib) before it runs on a modern interpreter. BlackArch installs it as `/usr/bin/necromant`.

## Typical use cases

### Match candidate vhost names against a set of web servers

Feed hostnames and server URLs to discover which servers still answer for which names.

```
necromant hostnames.txt urls.txt
```

Outcome: with `hostnames.txt` holding one name per line (e.g. `intranet.example.com`) and `urls.txt` holding `http://198.51.100.10`-style lines, the script lists the hostnames it is testing and servers it is probing on stderr, then prints one `hostname:http://IP` line on stdout for every hostname whose response fingerprint differs from the bogus-Host baseline — those are the virtual hosts found on that server.

*No corpus evidence — derived from upstream documentation (packaged Necromant.py source: usage, input-file formats, and fingerprint logic read directly).*
