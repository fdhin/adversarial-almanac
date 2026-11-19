---
name: "urx"
categories:
  - blackarch-webapp
version: "0.11.0.r115.g59c1cec"
homepage: "https://github.com/hahwul/urx"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "hahwul/urx"
github-stars: 194
github-pushed: "2026-11-07"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 687
gh-activity-checked: "2026-11-09"
---

# urx

> Rust CLI that collects a domain's known URLs from OSINT archives (Wayback Machine, Common Crawl, OTX and more), then filters, dedups and formats them for security work.

## What it's for

urx (hahwul) extracts URLs for a domain from OSINT archives: the keyless sources Wayback Machine, Common Crawl, AlienVault OTX, Arquivo.pt and URLScan by default, plus keyed providers (VirusTotal, ZoomEye, GitHub code search, BeVigil) that activate when API keys are configured. Every source is queried in parallel, then the results are filtered, tested and formatted client-side. It is archive-driven rather than a live crawler — it does not touch the target except when you explicitly enable live checks (--check-status, --check-title, --extract-links). Bug-bounty ergonomics are the point: scope files, archive-side and metadata date/MIME/status filters, extension/regex filters, presets like only-secrets, parameter views, wordlist output, a FUZZ-placeholder mode that pipes straight into ffuf or dalfox, and a SQLite cache with incremental rescans.

## Typical use cases

### Collect all archived URLs for a domain

The basic pull.

```
urx example.com
```

Outcome: queries the default providers (wayback, cc, otx) in parallel and prints the merged, deduplicated URL list, one per line; multiple domains can be passed as arguments or piped via stdin.

### Emit a wordlist of discovered paths

Feed content discovery.

```
urx example.com --subs -f wordlist -o words.txt
```

Outcome: includes subdomains and writes path segments and parameter names as a deduplicated, sorted wordlist to words.txt, ready for dirsearch-style tools.

### Filter down to potentially sensitive files

Hunt for secrets exposed in the past.

```
urx example.com -p only-secrets
```

Outcome: applies the only-secrets preset (/.env, /.git/config, id_rsa, *.pem and similar), leaving only archived paths that look like sensitive files; other presets include only-js, only-backup, only-config, only-api and no-images.

### Build a fuzzable URL set

Hand archived endpoints to a fuzzer.

```
urx example.com --fuzz-placeholder FUZZ | ffuf -w - -u FUZZ
```

Outcome: replaces every query-parameter value with FUZZ, keeping one URL per parameter signature; ffuf reads that URL list from stdin and fuzzes each parameter slot.

### Widen or restrict the provider set

Control the sources.

```
urx example.com --providers wayback,cc,otx,arquivo
```

Outcome: collection is restricted to the named providers; --all-providers enables every source for which a key is available, and --list-providers prints the full provider list with their key requirements.

No corpus evidence — derived from upstream documentation (README of hahwul/urx).
