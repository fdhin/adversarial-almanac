---
name: "hasher"
categories:
  - blackarch-cracker
version: "48.40173c5"
homepage: "https://github.com/ChrisTruncer/Hasher"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ChrisTruncer/Hasher"
github-stars: 52
github-pushed: "2021-12-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hasher

> Offline hash generator and hash-vs-plaintext verifier for ~25 formats, so found strings never touch an online "hash generator" site.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Hashes a plaintext into a wide range of formats an assessor meets in the field — MD4/MD5, NT, the SHA families, bcrypt, MySQL 4.1/5, MSSQL 2000/2005, Postgres-MD5, LDAP schemes, msdcc/msdcc2, Cisco PIX and Type 7 — or checks whether a hash you recovered matches a plaintext guess, entirely locally. The point is trust: during an assessment you should not paste passwords or hashes into a random web-based generator, and this replaces that workflow. It is generation and comparison only, not cracking: given just a hash it cannot produce anything, and comparison requires you to supply the candidate plaintext. The package is `hashes` on PyPI and installs the console command `hashes`; the current version is flag-driven (older releases were menu-driven).

## Typical use cases

### List the supported hash algorithms

```
hashes --list
```

Outcome: prints `Supported Hashing Algorithms:` followed by one `[*] <name>` line per loaded module (md5, nt, sha512, bcrypt, mssql2000, cisco_type7, ...) — the exact names to pass to `--hash-type`.

### Generate a hash offline

```
hashes -G --hash-type nt --plaintext 'Winter2026!'
```

Outcome: prints the hash type, the plaintext and the digest value. Use `--hash-type all` to emit the string in every supported format at once; salted formats take `--salt`, msdcc formats take `--username`, and crypt-style formats accept `--rounds`.

### Verify a hash against a plaintext guess

```
hashes -C --hash-type md5 --plaintext test --hash 098f6bcd4621d373cade4e832627b4f6
```

Outcome: prints `True - The hash ... and plaintext test match!` when the digest of the plaintext equals the supplied hash, or the corresponding `False ... do not match!` line when it differs. An unrecognized `--hash-type` prints an error asking to re-run with a valid type — check spelling against `--list` first.
