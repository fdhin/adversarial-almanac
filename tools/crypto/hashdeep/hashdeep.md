---
name: "hashdeep"
categories:
  - blackarch-crypto
version: "4.4"
homepage: "https://github.com/jessek/hashdeep"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-12-06"
build-system: autotools
license: GPL, custom:unknown
meta-checked: "2026-11-09"
github-repo: "jessek/hashdeep"
github-stars: 792
github-pushed: "2025-09-20"
github-archived: false
github-issues: 135
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashdeep

> The md5deep family's multi-algorithm front end: recursively hash directory trees and audit or filter them against lists of known hashes.

## What it's for

hashdeep computes several message digests for any number of files while optionally recursing through directory structure — MD5 and SHA-256 simultaneously by default, with SHA-1, Tiger, and Whirlpool available via `-c`. Beyond plain hashing it loads a list of known hashes (`-k`) in md5sum, NSRL, Hashkeeper, or iLook formats and either shows only files that match (`-m`), only files that do not (`-x`), or audits a directory against a previously recorded baseline (`-a`), failing on any added, changed, or missing file. That makes it the standard tool for forensic known-file filtering and for integrity baselines of whole trees. The same code installed under the names md5deep, sha1deep, sha256deep, tigerdeep, and whirlpooldeep runs single-algorithm; only the `hashdeep` name enables multi-hash and audit mode. It identifies nothing about password hashes — it digests files.

## Typical use cases

### Baseline a directory tree

```sh
hashdeep -r -c sha256 /var/www > www-baseline.txt
```

Outcome: writes a `%%%% HASHDEEP-1.0` manifest with `size,sha256,filename` per line covering every file under `/var/www`; keep this file as the reference for later audits. Add `-j 4` for four hashing threads on large trees.

### Audit a tree against its own baseline

```sh
hashdeep -a -k www-baseline.txt -r /var/www
```

Outcome: re-hashes the tree and prints `Audit passed` only when every file matched exactly one known hash with no collisions, additions, or missing files; `Audit Failed` otherwise (`-vv` lists the discrepancies, `-vvv` every file examined).

### Keep only files that match a known-hash list

```sh
hashdeep -m -k known.hsh -r /mnt/case
```

Outcome: prints just the files whose digests appear in `known.hsh` — identifying which collected files are already known.

### Filter out known files (negative match)

```sh
hashdeep -x -k known.hsh -r /mnt/case
```

Outcome: the inverse — lists only files whose hashes are NOT in the known list, the quick way to drop OS and other benign files from a forensic collection and leave the interesting remainder.

### Piecewise hashing for damaged media

```sh
hashdeep -p 512 -r /mnt/case
```

Outcome: hashes each file in fixed 512-byte chunks instead of whole-file, producing per-chunk digests that still compare across partially damaged, fragmented, or truncated files where a whole-file hash would never match.

*No corpus evidence — derived from upstream documentation.*
