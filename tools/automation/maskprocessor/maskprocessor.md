---
name: "maskprocessor"
categories:
  - blackarch-automation
version: "0.73"
homepage: "https://hashcat.net/wiki/doku.php?id=maskprocessor"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-14"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "hashcat/maskprocessor"
github-stars: 515
github-pushed: "2022-06-20"
github-archived: false
github-issues: 14
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# maskprocessor

> Standalone high-performance word generator driven by per-position mask charsets.

## What it's for

maskprocessor (the hashcat project's `mp64`/`mp32` binary) generates every password matching a mask — a per-position charset pattern such as `?u?l?l?l?l19?d?d` — which lets you enumerate human-pattern keyspaces (name + year, prefix + digits) that are astronomically smaller than full brute force. It is the mask engine hashcat uses, packaged as a standalone generator: use it to pipe candidates into crackers without mask support, or to precompute a targeted wordlist. Masks support the built-in charsets `?l ?u ?d ?s ?a ?b` plus four user-definable sets (`-1`..`-4`), hex charsets, increment mode and occurrence constraints. It only produces candidates to stdout or a file — it cracks nothing itself.

## Typical use cases

### Generate candidates for a name+year pattern

Express the human pattern as a mask with a custom charset.

```
mp64 -1 ?l?u ?1?l?l?l?l?l19?d?d
```

Outcome: streams every candidate from `aaaaaa1900` to `Zzzzzz1999` to stdout, one per line — pipe into a cracker or redirect to a file. (On BlackArch the binary is installed as `maskprocessor`.)

### Count a mask's keyspace before committing

Estimate the attack size without generating anything.

```
mp64 --combinations ?u?l?l?l?l?l?l?d?d?d?d
```

Outcome: prints just the number of candidates the mask produces — a quick sanity check on runtime before launching a long generation run.

### Cover a range of lengths with increment mode

Search all lengths from a minimum to a maximum.

```
mp64 -i 3:8 ?l?l?l?l?l?l?l?l
```

Outcome: generates the 3-character candidates first, then progressively longer ones up to length 8 (mask prefixes), mirroring hashcat's `--increment` behaviour.

### Write straight to a file with mixed custom charsets

Compose several custom charsets into one mask.

```
mp64 -o candidates.txt -1 ?dabcdef -2 ?l?u ?1?1?2?2?2?2?2
```

Outcome: writes every candidate from `00aaaaa` to `ffZZZZZ` into `candidates.txt`; `-s WORD`/`-l WORD` can resume a run at an exact start/stop candidate.

*No corpus evidence — derived from upstream documentation.*
