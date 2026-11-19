---
name: "haiti"
categories:
  - blackarch-crypto
version: "4.0.0.r47.g60879df"
homepage: "https://noraj.github.io/haiti/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/haiti"
github-stars: 1012
github-pushed: "2026-11-03"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 67
gh-activity-checked: "2026-11-09"
---

# haiti

> Ruby CLI and library that names a hash's type and hands you the matching hashcat mode and John the Ripper format.

## What it's for

Paste in a digest and haiti tells you which algorithm(s) could have produced it — over 675 hash types, including modern ones (SHA-3, Keccak, Blake2) and salted/application-specific variants. For each candidate it prints the hashcat mode number (`--hashcat-only`) and the John the Ripper format name, so it slots directly into a cracking workflow. It identifies only: it does not crack, and it does not verify a hash against plaintext.

## Typical use cases

### Identify a single hash

Get the candidate algorithms plus their hashcat and John references for one digest.

```
haiti d41d8cd98f00b204e9800998ecf8427e
```

Outcome: prints the digest, then each possible type with its hashcat `-m` mode and JtR format name; a 32-hex digest comes back as `[+] MD5` and friends. Feed the mode straight into hashcat or john.

### Include salted and application-specific variants

Widen the search to formats that embed a salt or wrap the digest (e.g. `md5($salt.$pass)` styles).

```
haiti -e d41d8cd98f00b204e9800998ecf8427e
```

Outcome: the candidate list grows to include salted/extended variants, each still annotated with hashcat/JtR references.

### Identify a hash piped from another tool

Chain haiti after a hasher without an intermediate file.

```
sha256sum file.txt | awk '{print $1}' | haiti -
```

Outcome: haiti reads the digest from stdin (`-`) and prints the same type table as for an argument — useful when scripting triage of dumped hashes.

### Identify every hash in a file

Triage a dump where each line is one digest.

```
haiti file hashes.txt
```

Outcome: each line of the file is classified in turn; the output flags the type per hash so mixed-algorithm dumps can be sorted before cracking.

### Show sample hashes for a known type

Go the other direction — see what a given algorithm's digests look like.

```
haiti samples crc32
```

Outcome: prints example digests for the named type (also accepts a hashcat or JtR reference instead of a name), which is handy when eyeballing a credential file.

*No corpus evidence — derived from upstream documentation.*
