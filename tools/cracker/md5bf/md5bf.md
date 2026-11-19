---
name: "md5bf"
gentoo-category: "app-crypt"
version: "0.1"
homepage: "http://www.edge-security.com/edge-soft.php"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# md5bf

> Small, fast MD5 cracker that brute-forces configurable ASCII ranges or walks a dictionary file.

## What it's for

md5bf is a compact C brute-forcer from Edge Security (with a hand-tuned x86 assembly MD5 core, hence the nasm build dependency) for recovering a plaintext from a single MD5 hash. Two methods are offered and can be combined: brute force over per-run character ranges given as decimal ASCII intervals (`-b [min-max]`) with word widths between `-w [min-max]`, or a plain dictionary file (`-d`). It handles one hash per invocation — it is not a hashfile batch cracker, has no GPU or rules support, and supports only MD5 (no NTLM or salted formats).

## Typical use cases

### Brute-force a hash over an ASCII character range

```
md5bf -m 5e93de3efa544e85dcd6311732d28f95 -b [97-120] -w [2-6]
```

Outcome: generates every combination of characters in ASCII 97–120 (`a`–`x`) for lengths 2 through 6 and prints the plaintext whose MD5 matches the `-m` target; `[97-122][48-57]`-style multiple ranges narrow candidate positions further. This exact invocation is the package's built-in smoke test.

### Run a dictionary attack

```
md5bf -m 098f6bcd4621d373cade4e832627b4f6 -d /usr/share/wordlists/rockyou.txt
```

Outcome: hashes each dictionary entry in turn and reports the matching word, or exhausts the list without a hit.

*No corpus evidence — derived from upstream documentation.*
