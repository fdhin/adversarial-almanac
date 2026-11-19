---
name: "smudge"
gentoo-category: "app-fuzz"
version: "0.8-r1"
homepage: "https://www.openrce.org/repositories/browse/peter"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# smudge

> SMUDGE — a Python port of SPIKE's block-based protocol fuzzing (Software Mutilation Utility and Data Generation Engine).

*No corpus evidence — derived from upstream documentation.*

## What it's for

SMUDGE (by nd@felinemenace.org, 2003-2004) reimplements Dave Aitel's SPIKE fuzzing model in Python: a protocol exchange is described as a script of primitives — `string()`, `plain()`, binary blocks, size words — and the engine mutates the marked parts (fuzz identifiers such as `<POLE>`, `<POLESTRING>`, `<POLEINT>`) with growing strings, format strings and hostile integers, in `topdown` or `bottomup` order and `single` or `multiple` (multi-request) mode. It can also fuzz file formats for client-side software and replay PDUs read from a directory. Ready-made fuzz scripts for HTTP, SMTP, POP, IMAP, IRC, Icecast, Mailman, Sambar and RealServer sit in `misc/`, and `misc/spike2smudge.py` converts existing SPIKE scripts. During its beta testing it found a newline-handling flaw in RatBox IRCD and a POST overflow in the Sambar web server. It is Python-2-era code with no documentation site — the shipped scripts are the de facto documentation.

## Typical use cases

### Print a skeleton fuzz script to adapt

Starts a new protocol fuzzer from the engine's own template.

```
python2 SMUDGE.py -t
```

Outcome: prints a ready-to-edit skeleton — `sm.setname(...)`, `sm.string()`/`sm.plain()` fuzz primitives, `sm.newreq()` request separators, ending in a `sm.run(host, port, order, mode)` call — that you save and adapt to the target protocol.

### Run a bundled protocol fuzzer

Uses one of the shipped example scripts against a lab service.

```
python2 misc/smtp.py
```

Outcome: replays a scripted SMTP conversation (HELO/MAIL FROM/DATA/QUIT) with every marked field mutated in turn; edit the `sm.run("...", 25, "topdown", "multiple")` line at the bottom of the script to point at your lab target before running.
