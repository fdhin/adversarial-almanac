---
name: "tftp-fuzz"
categories:
  - blackarch-fuzzer
version: "1337"
homepage: "http://nullsecurity.net/tools/fuzzer.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tftp-fuzz

> nullsecurity's "ftools" TFTP fuzzer that hammers a TFTP service with every opcode and command word at growing buffer lengths.

## What it's for

tftp-fuzz (by TheXero, nullsecurity, 2012) is a single-script Python 2 fuzzer for TFTP servers. It systematically crosses byte-valued opcodes with a list of TFTP command words (`get`, `put`, `mode`, `ascii`, `timeout`, ...) and character buffers that grow from 100 bytes toward a configurable maximum, exercising how the server parses malformed read/write/error requests. With `-e` the character set extends from printable ASCII to control and high bytes. It has no crash detection of its own — watch the target process while it runs.

## Typical use cases

### Fuzz the default TFTP port

```
tftp-fuzz -t 198.51.100.10
```

Outcome: iterates opcode × command × growing ASCII buffers against UDP port 69, ramping each buffer toward 10000 bytes; a server crash or hang during the run points at the request shape last sent.

### Extend coverage with every byte value on a custom port

```
tftp-fuzz -t 198.51.100.10 -p 6969 -m 500 -e
```

Outcome: the same enumeration against port 6969 with buffers capped at 500 bytes and the extended character set (control and high bytes) enabled, trading run length for per-string coverage.

*No corpus evidence — derived from upstream documentation.*
