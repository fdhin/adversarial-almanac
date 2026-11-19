---
name: "darkjumper"
categories:
  - blackarch-webapp
version: "5.8"
homepage: "http://sourceforge.net/projects/darkjumper/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# darkjumper

> Reverse-IP enumerator that finds every site co-hosted with a target and probes them for injection flaws.

## What it's for

DarkJumper answers "what else lives on this server?": it reverse-resolves a target to every domain hosted on the same machine, then optionally checks each co-hosted site for SQL injection, blind SQLi, LFI, RFI and RCE, scans CGI paths, guesses user accounts, or port-scans and banners the host. It is a 2007-era Python 2/Perl toolkit (by mywisdom); results are appended to a log file as it runs. Expect legacy behavior, not modern WAF-aware scanning.

## Typical use cases

### List co-hosted sites

```
darkjumper.py -t target.example.com -m reverseonly
```

Outcome: reverse-resolves the target and prints every website found hosted on the same server — the raw neighbour list that feeds further testing.

### Full vulnerability sweep of the neighbours

```
darkjumper.py -t target.example.com -m full
```

Outcome: after the reverse lookup, checks every co-hosted site for SQLi/blind SQLi/LFI/RFI/RCE and scans CGI paths, logging findings as they appear.

### Port-scan the host

```
darkjumper.py -t target.example.com -m portscan 1-1000
```

Outcome: sweeps the given port range on the target and reports the open ports found.

### Grab HTTP banner information

```
darkjumper.py -t target.example.com -m headerinfo
```

Outcome: fetches and prints the target's HTTP response headers, surfacing the server banner for service identification.

_No corpus evidence — derived from the packaged upstream source (usage text and mode list verified against darkjumper.py's option parser; the package installs it as /usr/bin/darkjumper.py, with `-p`, `-v` and `-a` modifiers for proxy, verbosity and auto-bruteforce)._
