---
name: "http-fuzz"
categories:
  - blackarch-fuzzer
version: "0.1"
homepage: "none"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# http-fuzz

> Minimal Perl HTTP request fuzzer that endlessly floods a host:port with randomly malformed request lines and header fields.

## What it's for

http-fuzz (by prdelka, packaged from the Pentoo overlay) is a single ~85-line Perl script with no options beyond its two arguments. It loops forever, each iteration assembling a random HTTP request from the GET/POST/HEAD verbs, 24 header-field names (Authorization, Range, Referer, Transfer-Encoding, ...), random control and high bytes, and random separators/terminators, sending it to the given host and port and printing how many bytes went out and came back. It is a quick lab robustness check for an HTTP daemon's request parser — not a coverage-guided tool, and it has no reporting beyond the byte counts.

## Typical use cases

### Fuzz an HTTP listener

Point it at a web server and let it run.

```
http-fuzz target.example.com 80
```

Outcome: connects repeatedly and prints one `[ Sent N byte(s) to host target.example.com(80/tcp) ]` / `[ Received N byte(s) ... ]` pair per malformed request; interrupt with Ctrl-C, then inspect the server's behavior and logs for crashes or hangs.

*No corpus evidence — derived from upstream documentation.*
