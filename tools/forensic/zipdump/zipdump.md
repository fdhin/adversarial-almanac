---
name: "zipdump"
categories:
  - blackarch-forensic
version: "0.0.21"
homepage: "https://blog.didierstevens.com/my-software/#zipdump"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "DidierStevens/DidierStevensSuite"
github-stars: 2576
github-pushed: "2026-10-13"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---
# zipdump

> Didier Stevens' ZIP analyzer: entry inventory, hex/ASCII dumps, password dictionary attacks, YARA scans and embedded-ZIP discovery.

## What it's for

zipdump.py is Didier Stevens' tool for examining ZIP containers the way oledump examines Office files (published on his blog; the packaged 0.0.21 is a standalone script). It inventories entries with their encryption flag and timestamps, optionally enriched with MD5, entropy and magic bytes; hex- or ASCII-dumps and extracts individual members by index or name; tries passwords — the default is the analysis convention `infected`, and `-P` runs a dictionary attack; and scans members with YARA rules or loadable decoders. `-f` locates embedded PK signatures inside arbitrary files, finding ZIP structures hidden in carriers. It inspects and carves selectively; wholesale extraction is what unzip is for. GitHub: the author's actively maintained suite repository DidierStevens/DidierStevensSuite carries `zipdump.py` at its top level (v0.0.36; the packaged zip is v0.0.21 from didierstevens.com) — his official GitHub home; the canonical distribution remains blog.didierstevens.com.

## Typical use cases

### Inventory a sample archive

See what a suspicious ZIP contains before extracting anything.

```
zipdump.py sample.zip
```

Outcome: prints one line per entry — index, filename, encrypted flag, timestamp — the quick surface view of the archive.

### Extended triage per entry

Rank members by content, not name.

```
zipdump.py -e sample.zip
```

Outcome: adds MD5, file size, entropy, unique-byte counts and magic bytes (hex and ASCII) per entry; encrypted or high-entropy members stand out as the ones worth deeper work.

### Dump or hexdump a selected member

Read one entry's bytes without unpacking the archive.

```
zipdump.py -x -s 2 sample.zip
```

Outcome: hex-dumps entry 2 to the terminal; `-a` gives an ASCII dump instead, and `-d` prints the raw bytes (`zipdump.py -d -s 2 sample.zip > member2.bin` writes the member to a file). `-s` also accepts a filename instead of an index.

### Open a password-protected sample

Get past sample passwords, including the usual ones.

```
zipdump.py -P passwords.txt -d -s 3 sample.zip > extracted.bin
```

Outcome: dictionary-attacks the archive with the given password file (`.` selects a built-in list; without `-P` the default password `infected` is tried) and dumps the selected member once a password matches.

### Find ZIP content embedded in a carrier file

Locate hidden or appended archives.

```
zipdump.py -f list carrier.bin
```

Outcome: scans a non-ZIP file for PK magic sequences and lists the offsets where embedded ZIP structures begin; re-run with a hit number in place of `list` to analyze the ZIP data found at that location.

*No corpus evidence — derived from upstream documentation.*
