---
name: "smtp-fuzz"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "none"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# smtp-fuzz

> Minimal Perl SMTP fuzzer that drives a full mail transaction with attack strings substituted into every field.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A single-file Perl fuzzer (Frank Berger, 2003) for SMTP servers: it walks a complete mail conversation — HELO, MAIL FROM, RCPT TO, DATA, QUIT — and on each pass replaces one field (HELO name, address local part, address domain, mail body) with the next entry from a built-in stack of attack strings. The strings are the classic SPIKE-derived set: multi-thousand-character runs of `A`/`1`/`"`/`.`/`<`/`%`/`%n`, path traversals aimed at `/etc/passwd` and `boot.ini`, format strings, and SQL/command-injection fragments. Every reply is checked against the expected SMTP status code (220/250/354) and printed with `OK:` or flagged as `SMTP error`, so a dead or misbehaving server is visible immediately. There are no options beyond host and port, no rate control, and no upstream website — the script ships inside the BlackArch package itself.

## Typical use cases

### Fuzz an SMTP listener end to end

Runs every mutation of every conversation field against one target.

```
smtp-fuzz 198.51.100.10 25
```

Outcome: for each attack string the script opens a fresh TCP connection to 198.51.100.10:25, drives HELO → MAIL FROM → RCPT TO → DATA → QUIT, and prints each reply after `OK:` or reports `SMTP error`; a target that stops replying mid-run (10-second read timeout) marks the crash — rerun and note the last payload printed. Point it at an isolated lab relay only, since injected strings can end up queued for delivery.
