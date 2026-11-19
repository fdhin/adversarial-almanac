---
name: "poly"
categories:
  - blackarch-webapp
version: "52.4e6f189"
homepage: "https://github.com/grCod/poly"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "grCod/poly"
github-stars: 30
github-pushed: "2017-10-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# poly

> Generates polymorphic encodings of PHP/ASP/ASPX webshells to slip past signature-based detection.

## What it's for

Re-encoding a webshell you already control so antivirus and upload filters that match the shell's literal text no longer recognize it. Feed it a shell type (`php`, `asp`, `aspx`) and optionally your own shell file; it emits a functionally equivalent copy built from one of four encodings — `b64` (base64 with random filler strings), `ord` (per-character `ord()` plus random offsets), `rnd` (random character mapping) or `rot` (row-rotation obfuscation) — optionally padded with junk code (`-j`). Caveats from the author: `rnd`/`rot` are not binary-safe (may emit unprintable characters), encoded ASPX shells can misbehave on some servers (prefer the default `shells/shell.asp` for ASP), and PHP works most reliably. Python 2.7 tool; the BlackArch package wraps `poly.py` as `poly-webshells` with bundled default shells under `/usr/share/poly/shells/`.

## Typical use cases

### Encode the default PHP shell

Produce a b64-polymorphic copy of the bundled PHP shell.

```
poly-webshells -c php
```

Outcome: writes the encoded shell (upstream form: `python2 poly.py -c php`); when `-p` is omitted the default shell from the `shells/` directory is used, and `b64` is the default encoding.

### Encode your own shell with a chosen encoder

Run a specific shell file through a stronger encoding.

```
poly-webshells -c aspx -p /usr/share/poly/shells/shell.aspx -e rot
```

Outcome: reads the given shell file, applies the `rot` encoding (`-e b64|ord|rnd|rot`) and writes the polymorphic result; the shell type must match the file (`php`, `asp` or `aspx`).

### Add junk code on top of the encoding

Layer random junk statements around the encoded payload.

```
poly-webshells -c php -e ord -j
```

Outcome: the generated shell interleaves the `ord()`-encoded payload with junk code (`-j`), further breaking static signatures that key on structure or density.

*Provenance: no corpus evidence — derived from upstream documentation (README + `poly.py` argparse, verified 2026-11).*
