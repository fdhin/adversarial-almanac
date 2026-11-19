---
name: "boofuzz"
categories:
  - blackarch-fuzzer
version: "v0.4.2.r44.g41a808e"
homepage: "https://github.com/jtpereyda/boofuzz"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "jtpereyda/boofuzz"
github-stars: 2373
github-pushed: "2026-09-21"
github-archived: false
github-issues: 101
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# boofuzz

> Python 3 network protocol fuzzing framework — the maintained fork of and successor to Sulley, with session management, extensible instrumentation and test-case recording.

## What it's for

boofuzz (Joshua Pereyda) is a fork of and the successor to the Sulley fuzzing framework (see the [[sulley]] note in this category): Sulley had fallen out of maintenance, and boofuzz carries its design forward with bug fixes, online documentation and a focus on extensibility. You describe a protocol as a tree of primitives (Request, Block, Group, String, Delim, Static), attach it to a Target with a connection object — TCP, UDP, serial, raw ethernet/IP-layer, UDP broadcast — and call `session.fuzz()`; boofuzz mutates each primitive in turn, records every test step and result (CSV export included), and detects failures through companion monitors shipped in the repository (process_monitor_unix.py/process_monitor.py for target processes, network_monitor.py for pcap-level checks). Reach for it when you can structurally describe a network service or serial device; it is not a browser or file-format fuzzer.

## Typical use cases

### Fuzz an HTTP endpoint

Describe a request declaratively and let boofuzz mutate every field.

```python
from boofuzz import *

session = Session(target=Target(connection=TCPSocketConnection("198.51.100.10", 80)))

req = Request("HTTP-Request", children=(
    Block("Request-Line", children=(
        Group(name="Method", values=["GET", "HEAD", "POST"]),
        Delim(name="space-1", default_value=" "),
        String(name="URI", default_value="/index.html"),
        String(name="HTTP-Version", default_value="HTTP/1.1"),
        Static(name="CRLF", default_value="\r\n"),
    )),
))

session.connect(req)
session.fuzz()
```

Outcome: run it with python3 — boofuzz mutates each primitive (method, URI, version, delimiters) against 198.51.100.10:80, prints per-case status, and records all steps and results for review and CSV export.

### Fuzz a UDP service

Same model over datagrams, using the legacy Sulley-style API.

```python
from boofuzz import *

session = Session(target=Target(connection=UDPSocketConnection("198.51.100.10", 69)))

s_initialize("RRQ")
s_static("\x00\x01")
s_string("filename", name="Filename")
s_static("\x00")
s_string("netascii", name="Mode")
s_static("\x00")

session.connect(s_get("RRQ"))
session.fuzz()
```

Outcome: sends mutated TFTP read requests (opcode 0x0001, filename, mode) to the target's UDP port 69; the `s_*` API is fully equivalent to the block API — both appear throughout the shipped examples directory.

*No corpus evidence — derived from upstream documentation.*
