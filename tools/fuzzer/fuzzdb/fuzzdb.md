---
name: "fuzzdb"
categories:
  - blackarch-fuzzer
version: "475.5656ab2"
homepage: "https://github.com/fuzzdb-project/fuzzdb"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "fuzzdb-project/fuzzdb"
github-stars: 9007
github-pushed: "2023-11-10"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fuzzdb

> Open dictionary of fault-injection payloads, predictable resource locations, and response-matching patterns for black-box testing.

## What it's for

FuzzDB (by Adam Muntner) is a pure data collection — "an application security scanner, without the scanner". It is the original open corpus of categorized attack payloads (command injection, SQL and NoSQL injection, traversal, XSS, CRLF, file-upload and authentication bypasses, debug-parameter names, null-byte variants), predictable resource locations sorted by platform and application, regex dictionaries for matching interesting server responses (error messages, PII, credit cards), web backdoors in several languages, and username/password wordlists. It ships no executable CLI: you feed its text files into whatever tool you already use — Burp Intruder, OWASP ZAP (which has a FuzzDB extension), ffuf/wfuzz-style wordlist tools, or plain grep against captured responses. Expect antivirus alerts on a checkout; the upstream advises not to store it on servers because of local-file-include risk. On BlackArch it is installed under `/usr/share/fuzzdb/`.

## Typical use cases

### Brute-force predictable paths with a discovery list

Use a FuzzDB discovery dictionary as the wordlist for a content-discovery tool.

```
ffuf -u https://target.example.com/FUZZ -w /usr/share/fuzzdb/discovery/predictable-filepaths/UnixDotfiles.txt -mc 200,301
```

Outcome: ffuf requests each predictable filepath from the FuzzDB discovery corpus and prints the ones returning 200/301 — candidate log files, dotfiles, and administrative paths worth reviewing. Swap in `KitchensinkDirectories.txt` or `Randomfiles.txt` for wider sweeps.

### Triage captured responses with detection signatures

Grep saved responses for known SQL-injection error signatures using FuzzDB's detect lists.

```
grep -if /usr/share/fuzzdb/attack/sql-injection/detect/Generic_SQLI.txt responses.txt
```

Outcome: any line in `responses.txt` matching a FuzzDB SQLi detection string is printed with its filename — a fast way to confirm injection points after a payload sweep. The `regex/` directory (e.g. `regex/errors.txt`) serves the same purpose as extended-regex patterns.

### Pull a single list without a full checkout

Fetch one dictionary straight from the raw repository when a checkout isn't at hand.

```
curl -s https://raw.githubusercontent.com/fuzzdb-project/fuzzdb/master/discovery/predictable-filepaths/UnixDotfiles.txt
```

Outcome: the list is printed to stdout, ready to pipe into another tool or save locally — useful when you need exactly one corpus file on a box where cloning the whole collection is unnecessary or undesirable.

### Browse the corpus for a class of attack

Enumerate what the collection covers before choosing payloads.

```
find /usr/share/fuzzdb -maxdepth 2 -type d
```

Outcome: prints the top-level corpus layout — `attack/`, `discovery/`, `regex/`, `web-backdoors/`, `wordlists-user-passwd/`, `wordlists-misc/` — so you can drill into the category matching the bug class you are testing; most directories carry their own `README.md` with usage notes.
