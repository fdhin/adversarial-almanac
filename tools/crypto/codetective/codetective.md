---
name: "codetective"
categories:
  - blackarch-crypto
version: "45.52b91f1"
homepage: "https://www.digitalloft.org/init/plugin_wiki/page/codetective"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "blackthorne/Codetective"
github-stars: 135
github-pushed: "2026-09-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# codetective

> Identifies hash formats, encodings and other artefacts from traces of their representation — and can auto-decode layered encodings and classical ciphers.

## What it's for

Codetective examines strings, files, whole directories or stdin and reports which algorithm or encoding most likely produced what it sees: MD/SHA family digests, Windows LM/NTLM/SAM entries, Unix shadow formats, Django/Joomla/phpBB3/WordPress hashes, MySQL/MSSQL formats, JWTs, UUIDs, base64, credit cards and more. Each finding carries a numeric certainty score (0–100) and can be filtered by source, minimum certainty or custom validators. Since version 0.9.2 a crack mode (`-c`) goes one step further and tries to actually decode the input, chaining encodings and classical ciphers (base64 → ROT13 → Bacon, single-byte XOR, rail fence, ...) up to three layers deep and ranking candidates by how much they look like real text. It runs standalone on Python 3.8+ with no third-party packages, or as a Volatility plugin to scan memory dumps.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Identify the format of a hash

```
python3 codetective.py '79b61b093c3c063fd45f03d55493902f'
```

Outcome: prints every algorithm whose representation matches the string (e.g. MD5, MD4, NTLM, Joomla MD5, base64-decoded), one finding per line — the starting point for choosing how to crack or interpret the artefact.

### Narrow candidates with source filters

```
python3 codetective.py -t win -a '79B61B093C3C063FD45F03D55493902F:*'
```

Outcome: only Windows-family formats are reported (LM, NTLM, SAM entries) with expanded detail; filters `win,web,unix,db,personal,crypto,other` can be combined comma-separated to cut false positives when the origin of the data is known.

### Scan a directory tree for high-confidence artefacts

```
python3 codetective.py -r -d mypath/ -m 80 -fp '*.txt'
```

Outcome: walks `mypath/` recursively, scans only `*.txt` files in bounded overlapping windows (large files and pipes never fill memory) and prints findings with certainty ≥ 80 together with their byte offsets from the start of each file.

### Auto-decode layered encodings and classical ciphers

```
python3 codetective.py -c 'synt{ebg13_vf_rnfl}'
```

Outcome: instead of identifying the input, tries every supported encoding and cipher and chains of them across all CPU cores, then prints ranked candidates with a 0–100 plausibility score — for this input, a full-score `rot13` candidate decoding to plaintext. Works the same on files (`-c -f file`) and stdin (`echo ... | codetective.py -c -s`).

### Steer the cracker with a known answer format

```
python3 codetective.py -c -f challenge.txt -cd 3 -cr 'flag\{.*\}' -ct 10
```

Outcome: depth-3 chained decoding in which candidates matching the given regex receive a large score boost; prints the top 10 candidates with the chain that produced them (e.g. `input_base64 → bacon_cipher`) and the decoded text.
