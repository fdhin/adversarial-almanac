---
name: "hashcat-utils"
categories:
  - passwords
version: "1.10-1"
homepage: "https://github.com/hashcat/hashcat-utils"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-02-08"
build-system: make
meta-checked: "2026-11-09"
github-repo: "hashcat/hashcat-utils"
github-stars: 1635
github-pushed: "2026-10-27"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# hashcat-utils

> Companion suite for hashcat — ~25 single-purpose stdin/stdout utilities for converting captures, massaging wordlists and rules, and doing mask keyspace math.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Small C programs maintained by the hashcat project, each doing exactly one transformation so they chain with pipes when preparing attacks: extracting handshakes out of packet captures into hashcat formats, generating and permuting candidate words, measuring how many candidates a mask produces, or filtering and splitting dictionaries. They prepare input for hashcat (or John the Ripper) and never crack anything themselves — no GPU code, no candidate testing. The built set includes cap2hccapx, combinator/combinator3/combinatorX, combipow, ct3_to_ntlm, cutb, expander, gate, generate-rules, hcstat2gen, keyspace, len, mli2, morph, ngramX, permute, permute_exist, prepare, req-include/req-exclude, rli/rli2, rules_optimize, splitlen and strip-bsn/strip-bsr. Since every utility speaks stdin/stdout, pipes are the intended way to drive them.

## Typical use cases

### Convert a WPA capture into a crackable hash file

```
cap2hccapx capture.pcap output.hccapx
```

Outcome: writes one entry per handshake detected in the capture to `output.hccapx`, printing which networks/BSSIDs were found; append an ESSID argument to keep only that network. The resulting file is fed to hashcat's WPA mode.

### Combine two wordlists without materializing the product

```
combinator adjectives.txt nouns.txt > candidates.txt
```

Outcome: every word of `adjectives.txt` is concatenated with every word of `nouns.txt` (cartesian product, dictionary order) and streamed to stdout — pair with a pipe into a cracker or `head` to preview instead of writing a giant file. Use `-` as the first "file" to stream another pipe's output through the combination.

### Compute the keyspace of a mask

```
keyspace ?u?l?l?l?l?d?d?d
```

Outcome: prints the exact number of candidates the mask generates (lowercase `?l` = 26, digits = 10, and so on multiplied out) — the number hashcat's `--skip`/`--limit` milestones are measured against when splitting a big attack across machines or sessions.

### Split a wordlist into per-length files

```
splitlen split/ < rockyou.txt
```

Outcome: creates files under `split/` named by password length, each holding only the words of that length — useful for length-constrained targets or for parallelizing jobs by length class.

### Subtract one list from another

```
rli master.txt fresh.txt already-cracked.txt
```

Outcome: writes every line of `master.txt` that does not appear in `already-cracked.txt` to `fresh.txt`; additional removal files can be appended as extra arguments. Typically used to drop already-cracked hashes/words before the next run.
