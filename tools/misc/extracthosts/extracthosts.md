---
name: "extracthosts"
categories:
  - blackarch-misc
version: "17.8fdff9e"
homepage: "https://github.com/bwall/ExtractHosts"
packaged-by: [blackarch]
blackarch-builddate: "2021-03-27"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "bwall/ExtractHosts"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# extracthosts

> `eh` — pull hostnames and IP addresses out of any file that stores them in plaintext.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ExtractHosts (by Brian Wallace, bwall) installs the command `eh`: it scans files, directory trees, or standard input and extracts IPv4 addresses, IPv6 addresses, and domain names from anything that keeps them in plaintext — text and config files, documentation, PE binaries, malware samples. It was built for malware triage: recovering C2 domains and IPs from samples without running them. Filters select which result types to emit, and it can attribute each hit to the file it came from. It does not decode obfuscated, packed, or encoded indicators — if the host is not stored as plaintext in the file, it will not appear. The upstream repository is gone (the tool is a Python 2-era project, archived on the Wayback Machine); expect to run it from a packaged copy.

## Typical use cases

### Extract hosts from a fetched page

Pipe any text through `eh` for a deduplicated host list.

```
curl -s https://example.com | eh -d
```

Outcome: prints every hostname and IP found in the input, duplicates suppressed (`-d`) — a one-liner for collecting domains referenced by a page or document.

### Pull indicators from a sample

Recover embedded hosts from a binary without executing it.

```
eh sample.bin
```

Outcome: lists the plaintext domains and IP addresses stored in the file (e.g. a malware sample's C2 endpoints), one per line.

### Scan a directory tree with attribution

Map every hit back to the file that contained it.

```
eh -drf samples/
```

Outcome: recursively (`-r`) walks the directory, and prefixes each result with the file path (`-f`), duplicates hidden per file (`-d`) — a per-file inventory suitable for diffing sample families.

### Filter to a single result type

Keep only the indicator class you want.

```
eh -D page.html
```

Outcome: emits domain results only; use `-4` or `-6` instead to restrict output to IPv4 or IPv6 addresses respectively.
