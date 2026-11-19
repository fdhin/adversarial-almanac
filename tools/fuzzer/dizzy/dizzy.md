---
name: "dizzy"
categories:
  - blackarch-fuzzer
version: "2.0"
homepage: "http://www.c0decafe.de/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ernw/dizzy"
github-stars: 69
github-pushed: "2018-07-25"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dizzy

> A stateful and stateless network-protocol fuzzing framework by Daniel Mende.

## What it's for

dizzy fuzzes network services from protocol descriptions instead of raw
payloads. Individual packets are described in `.dizz` files (Python snippets
defining fields with sizes, endianness and per-field fuzz modes `none`,
`std`, `full`), and multi-step stateful exchanges in `.act` files that chain
packets and carry response bytes forward between them. A job file (`.conf`)
binds it together: which interaction or packet set to fuzz, the output
session type (`session.tcp`, `session.stdout-hex`, ...), target address and
port, and an optional probe (e.g. ICMP) that checks the target survived each
step. Modules for real protocols (SMB2, DNS, and more) plus a demo module
ship with it, and a Wireshark Lua plugin can export parsed captures as
`.dizz` files. It is Python 3 and suits structured protocol work where blind
byte flipping dies on the first checksum.

## Typical use cases

### List the loaded modules

```
dizzy -l
```

Outcome: prints every loaded module with its contents — dizz/act files, job
files, sessions and probes — which is the inventory to pick or verify a job
from before running it.

### Run a fuzzing job

Point dizzy at a job file; that single file is all it needs.

```
dizzy demo.conf
```

Outcome: dizzy loads the module referenced by the job's `file` setting and
starts mutating through the configured output session, printing a progress
line every 60 seconds (configurable with `-d SEC`); the target's reaction is
observed on the wire or by the configured probe.

### Run a job against a different target

Override job-file settings on the command line without editing the file.

```
dizzy -o output.target_host=198.51.100.10 -o output.target_port=445 demo.conf
```

Outcome: the job runs exactly as configured in `demo.conf` except the output
session points at 198.51.100.10:445 — the `-o option.name=value` form accepts
any job-file option and can be repeated.

### Resume a campaign partway through

```
dizzy -s 1500 demo.conf
```

Outcome: the fuzzer skips all fuzzing steps before step 1500 and continues
from there, letting a long campaign resume after a crash or pause without
redoing completed mutations.

*No corpus evidence — derived from upstream documentation.*
