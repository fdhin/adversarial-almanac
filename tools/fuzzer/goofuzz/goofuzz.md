---
name: "goofuzz"
categories:
  - blackarch-fuzzer
version: "2.0.r1.gc32df39"
homepage: "https://github.com/m3n0sd0n4ld/GooFuzz"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "m3n0sd0n4ld/GooFuzz"
github-stars: 1594
github-pushed: "2025-12-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# goofuzz

> Google-dorking reconnaissance via the Programmable Search API — enumerates indexed files, paths and subdomains without sending any request to the target server.

## What it's for

GooFuzz (installed as `goofuzz`) is a Bash script by M3n0sd0n4ld that applies advanced Google search techniques to enumerate files, directories and subdomains of a domain. All queries go to Google's Programmable Search API, so the target web server is never contacted and its logs stay clean. It requires a keys file (one `CX_ID,API_KEY` pair per line) created from a free Google Cloud API key and a Programmable Search Engine ID. Results reflect Google's index, not the live server — an indexed path may no longer exist (or may have appeared) on the host.

## Typical use cases

### Find indexed files by extension

Enumerate documents of given types that Google has indexed under a domain.

```
goofuzz -t example.com -k keys.txt -e pdf,doc,bak -p 3 -o results.txt
```

Outcome: prints indexed URLs grouped under per-extension headers ("Extension: pdf", ...) and writes the result list to `results.txt`; raise `-p` to page deeper into the index.

### Enumerate subdomains

Harvest the subdomain footprint of a domain from the Google index.

```
goofuzz -t example.com -k keys.txt -s -p 10 -d 5
```

Outcome: prints discovered subdomains one per line under "Subdomains found:"; `-d 5` delays each request by 5 seconds to stay within API quotas.

### Check paths and filenames without touching the server

Test whether interesting paths, words or filenames are indexed, using a wordlist or a comma-separated list.

```
goofuzz -t example.com -k keys.txt -w wordlists/words-100.txt
```

Outcome: lists indexed URLs matching the supplied words — a log-free alternative to web content brute-forcing; exclude noisy hosts with `-x dev.example.com` (single host or an exclusion file).

### Locate documents containing sensitive content

Search indexed file contents for keywords, including text embedded in PDFs and images.

```
goofuzz -t example.com -k keys.txt -c password -e pdf
```

Outcome: prints indexed documents whose content matches the keyword, restricted to the given extensions — useful for finding published credentials or internal material.

### Route searches through a proxy

Keep API traffic behind an intermediary.

```
goofuzz -t example.com -k keys.txt -e pdf -r http://proxy.example.com:8080
```

Outcome: search-engine requests are sent through the specified `[protocol://]host[:port]` proxy; results print as usual.

*No corpus evidence — derived from upstream documentation.*
