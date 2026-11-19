---
name: "xxxpwn"
categories:
  - blackarch-webapp
version: "10.27a2d27"
homepage: "https://github.com/feakk/xxxpwn"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "feakk/xxxpwn"
github-stars: 33
github-pushed: "2026-01-29"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# xxxpwn

> The XPath eXfiltration eXploitation tool — recovers the backend XML document through a blind XPath 1 injection point using optimized queries.

## What it's for

xxxpwn (by feakk, "1.0 kiwicon release") is designed for blind optimized XPath 1 injection attacks: given a sample HTTP request whose vulnerable parameter is marked with `$INJECT`, and a keyword that only appears in the response when the injected XPath expression is true, it reconstructs the remote XML document — by default the entire backend document — using binary-search character discovery and a battery of query optimizations (global node counts, charset optimization, whitespace normalization, XML match reuse). It targets XPath injection only, works over plain HTTP or SSL, and ships ready-made injection request files for previously disclosed vulnerabilities (Sitecore, Umbraco, XMLMAO and the xcat test application) under `/usr/share/xxxpwn/attacks/`. The repository is archived (January 2026); the successor-style fork [xxxpwn-smart](../xxxpwn-smart/xxxpwn-smart.md) adds predictive-text recovery.

## Typical use cases

### Recover the full document from a blind injection

```
xxxpwn -i /usr/share/xxxpwn/attacks/sample_inject_file.txt -m "Welcome" target.example.com 80
```

Outcome: xxxpwn replays the sample request against `target.example.com:80`, uses `Welcome` as the boolean oracle, and streams the recovered XML document structure and values to the terminal.

### Exploit a known-vulnerable application

```
xxxpwn -i /usr/share/xxxpwn/attacks/inject_sitecore.txt -m "Error" target.example.com 80 --summary
```

Outcome: the bundled injection file for the disclosed vulnerability is used directly, and `--summary` prints only the XML summary (node counts and structure) instead of every value.

### Parallelize the extraction

```
xxxpwn -i request.txt -m "Welcome" -t 20 target.example.com 80
```

Outcome: the recovery is split across 20 threads, which shortens long extractions on targets that tolerate the extra request rate.

### Search the document for a string

```
xxxpwn -i request.txt -m "Welcome" --search password target.example.com 80
```

Outcome: instead of (or before) full recovery, xxxpwn prints all recovered values containing `password`, which quickly locates credentials in large documents.

*No corpus evidence — derived from upstream documentation (README and the packaged `xxxpwn.py` argparse of feakk/xxxpwn).*
