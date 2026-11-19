---
name: "hashpeek"
categories:
  - blackarch-crypto
version: "v0.2.2.r1.gd8fa678"
homepage: "https://github.com/ph4mished/hashpeek"
packaged-by: [blackarch]
blackarch-builddate: "2025-08-30"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "ph4mished/hashpeek"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# hashpeek

> Go-based hash identifier and extractor that pulls hashes out of files and structured data and emits JSON or CSV instead of prose.

## What it's for

hashpeek classifies hash types like older identifiers (hashid and friends) but focuses on extraction at scale: it accepts a single hash, a file, stdin, or filenames piped in, and reports the hash types found grouped by type. It was written to close the gaps of existing identifiers — it takes stdin for scripting, outputs greppable JSON/CSV rather than decorated text, can extract hashes from logs, dumps, and structured data such as shadow files without per-hash output spam, and can slice a chosen field out of delimited lines before matching. The upstream repository has been deleted (the vault link and BlackArch package remain); every flag below is verified against the last archived source.

## Typical use cases

### Classify a single hash

```sh
hashpeek -x '$6$saltstring$base64digest'
```

Outcome: prints the type classification for the supplied hash string; add `-vb` for detailed per-hash results (algorithm details, hashcat/John formatting information).

### Extract and classify hashes from a file

```sh
hashpeek -f /etc/shadow
```

Outcome: scans the file and reports each hash found, grouped by hash type — structured sources like shadow files yield per-account results rather than a wall of redundant matches.

### Get machine-readable output

```sh
hashpeek -f dump.txt -json
hashpeek -f dump.txt -csv
```

Outcome: same extraction with results as JSON records or CSV rows — pipeable into `jq` or a spreadsheet, with no prose formatting to strip out.

### Analyze hashes from stdin

```sh
cat hashes.txt | hashpeek -x -
```

Outcome: reads hashes directly from stdin and prints classifications as they arrive — the scripting path that many older identifiers lack. `-f -` likewise reads filenames from stdin.

### Slice a field before matching

```sh
hashpeek -f creds.txt -trunc '{1} `:::`'
```

Outcome: before matching, splits each line on the `:::` delimiter and keeps the 0-based index-1 segment — extracting the hash field from `user:::hash:::salt`-shaped lines.

### Skip comment or junk lines

```sh
hashpeek -f dump.txt -i '#'
```

Outcome: lines beginning with `#` are ignored during extraction, keeping config-style files from producing junk matches.

*No corpus evidence — derived from upstream documentation (archived source; upstream repository deleted).*
