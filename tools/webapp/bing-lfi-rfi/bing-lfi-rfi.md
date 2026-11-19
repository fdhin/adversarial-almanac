---
name: "bing-lfi-rfi"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/121590/Bing-LFI-RFI-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# bing-lfi-rfi

> Bing dork harvester that mass-checks result URLs for LFI and RFI.

## What it's for

A single-file Python 2 script ("Bing LFI-RFI Searcher" by Miyachung / Janissaries) distributed via Packet Storm. It takes a file of Bing search dorks, harvests result URLs that contain a query string, then probes each parameter for local file inclusion by appending an `/etc/passwd` traversal path (with and without a null byte) and checks for `root:x` in the response; URLs that fail that are tested for remote file inclusion by appending a remote shell URL and looking for `safe_mode` in the response. Findings are printed and appended to `results.txt` in the working directory. It is a legacy mass-checker: it needs Python 2 (urllib2), has no options beyond the dork file and thread count, and the RFI shell URL is hardcoded.

## Typical use cases

### Check a dork list for LFI/RFI at scale

Run the searcher over a prepared dork file.

```
bing-lfi-rfi dorks.txt 10
```

Outcome: for each dork in `dorks.txt`, pages through Bing results with 10 worker threads, prints `[+] ... /etc/passwd readed` or `[+] remote file include vulnerability works!` lines for vulnerable URLs, and appends every finding to `results.txt`; non-vulnerable URLs are reported as checked.

No corpus evidence — derived from the packaged upstream script (Packet Storm `binglfirfi.txt`).
