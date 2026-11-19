---
name: "omnihash"
categories:
  - blackarch-crypto
version: "70.870e9ae"
homepage: "https://github.com/Miserlou/omnihash"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Miserlou/omnihash"
github-stars: 247
github-pushed: "2016-12-18"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# omnihash

> Hashes strings, files, stdin streams and URLs with every supported algorithm at once — the opposite of a hash identifier.

## What it's for

omnihash (aliased as `oh`) computes MD4, MD5, the SHA/SHA2/SHA3 families,
RIPEMD160, whirlpool, BLAKE2 and more for a given input in one shot. Its niche
is reverse engineering and CTF-style identification work: when you know
something is being hashed but not which algorithm, hash the candidate input
once and match the known digest against the full table instead of guessing
algorithm by algorithm. Inputs can be strings, files, URLs (content is
fetched and hashed) or a stdin stream; multiple arguments are processed in one
run. It is a read-only hashing utility — no cracking, no brute forcing.

## Typical use cases

### Hash a string with every algorithm at once

Identify which algorithm produced a digest you already have.

```
omnihash "correct horse battery staple"
```

Outcome: a `name: digest` table covering MD4/MD5/SHA1/SHA2/SHA3/RIPEMD160/
whirlpool/BLAKE2 and aliases; scan it for the digest you are trying to
attribute.

### Match only a known digest fragment

Cut the table down to the algorithm(s) whose output contains a known prefix.

```
omnihash "correct horse battery staple" -m 9cc2
```

Outcome: prints only the matching lines (e.g. `MD5: 9cc2ae8a...`), turning
attribution into a one-line answer.

### Hash a file, a stream, or many files

Process files without retyping per-algorithm commands.

```
cat firmware.bin | omnihash
omnihash *.txt
```

Outcome: hashes the piped stream (or every file argument) across all
algorithms, one table per input.

### Emit JSON and filter algorithm families

Get machine-readable output restricted to the algorithm families you care
about.

```
omnihash sample.bin -f sha2 -f sha5 -j
```

Outcome: a JSON object mapping only the selected family's algorithm names
(SHA224/SHA256/SHA384/SHA512 for `sha2`/`sha5`) to their digests, suitable for
piping into `jq`.

### Include CRC-style checksums

Compare against CRC32-style values too.

```
omnihash sample.bin -c
```

Outcome: the full digest table plus CRC-8/16/24/32/64 variants (CRC-32,
JAMCRC, XMODEM, ...) in hex — useful when a protocol or file format stores a
CRC instead of a cryptographic hash.

*No corpus evidence — derived from upstream documentation.*
