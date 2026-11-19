---
name: "passe-partout"
categories:
  - blackarch-cracker
version: "0.1"
homepage: "http://www.hsc.fr/ressources/outils/passe-partout/index.html.en"
packaged-by: [blackarch]
blackarch-builddate: "2017-01-14"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "kholia/passe-partout"
github-stars: 64
github-pushed: "2017-12-30"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# passe-partout

> Dumps RSA and DSA private keys straight out of a live process's memory by scanning for OpenSSL key structures.

*No corpus evidence — derived from upstream documentation.*

## What it's for

passe-partout (HSC — Nicolas Collignon and Jean-Baptiste Aviat) recovers SSL/TLS private keys from any running process linked against OpenSSL: it ptrace-attaches to a target PID, walks its memory mappings looking for the byte patterns of OpenSSL RSA and DSA key structures, validates each candidate key it finds, and writes the valid ones to disk as PEM files. Targets tested upstream include sshd and ssh-agent, Apache httpd, nginx, Node.js, Tomcat (with APR) and Python web servers — anywhere the private key lives in process memory. It matches the OpenSSL 1.0.x structure layout (building against OpenSSL 1.1.x does not work, per the author), so it fits older service stacks; it does not attack encrypted key files on disk, hardware keys, or non-OpenSSL crypto libraries.

## Typical use cases

### Extract keys from a running service

Recover the TLS keys a daemon holds in memory.

```
passe-partout 1234
```

Outcome: attaches to PID 1234, scans every readable memory mapping, prints `found RSA key @ 0x...` / `found DSA key @ 0x...` as candidates appear, validates them, and saves each confirmed key as a PEM file named `id_rsa-0.key` or `id_dsa-0.key` (numbering up when files already exist) in the current directory, announcing each save with `[X] Key saved to file ...`.

### Restrict scanning to one address range

Cut scan time by pointing at a known region from the process's memory map.

```
passe-partout -a 7f0000010000-7f0000020000 1234
```

Outcome: scans only the given hex address range of the target (a bare address without `-to` scans a single key-sized window), skipping the rest of the mappings — useful when `/proc/1234/maps` already shows where the SSL structures live. Both bounds must be aligned or the tool aborts with `unaligned map range`.

### Watch key validation in detail

Diagnose a target that yields no keys.

```
passe-partout -v 1234
```

Outcome: raises verbosity (the flag is repeatable), printing OpenSSL BIGNUM and key-structure details as candidates are examined, plus warnings for invalid or unverifiable keys — helping to tell "no keys in memory" from "keys present but structurally unfamiliar".
