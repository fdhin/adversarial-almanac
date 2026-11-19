---
name: "fuzzowski"
categories:
  - blackarch-fuzzer
version: "41.e39f665"
homepage: "https://github.com/nccgroup/fuzzowski"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "nccgroup/fuzzowski"
github-stars: 793
github-pushed: "2024-01-29"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fuzzowski

> NCC Group's network protocol fuzzer — a Sulley/boofuzz fork with built-in printing (LPD/IPP) and ICS protocol fuzzers.

## What it's for

Fuzzowski (by Mario Rivas / NCC Group) is a Python 3 network protocol fuzzer forked from boofuzz (itself a fork of Sulley). It is deterministic rather than random: you describe requests with typed primitives (Sulley-style) or use raw request templates with injection points, and it walks a finite space of mutations. Its distinguishing features target the workflow around the fuzzing itself: an interactive console (built on prompt_toolkit) to pause, review, and retest "suspect" test cases; automatic saving of marked crashes as ready-made Python PoC scripts; monitor modules that probe the target and flag odd behavior; and restarter modules that recover the target after a crash — including power-cycling a smart plug. Built-in fuzzers cover LPD (fully), IPP, BACnet, and Modbus (partially), plus COPS, DHCP, NETCONF, telnet CLI, TFTP, and a generic raw mode; it grew out of NCC Group's printer research, so IPP support is a first-class citizen. It is not a coverage-guided binary fuzzer — it speaks plain sockets (TCP/UDP/SSL) to a service.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz an IPP printer

Sweep the mutations of one IPP operation against a network printer.

```
python -m fuzzowski printer.example.com 631 -f ipp -r get_printer_attribs --restart smartplug
```

Outcome: fuzzowski connects to port 631, sends mutated `get_printer_attribs` requests, and marks misbehaving test cases as suspects in its console; the smartplug restarter power-cycles the printer when it stops responding so the run continues unattended. On BlackArch the installed `fuzzowski` wrapper runs this without the `python -m` prefix.

### Fuzz a simple text protocol with raw templates

Skip protocol definitions entirely when a request is just a line of text.

```
python -m fuzzowski target.example.com 79 -f raw -r '{{root}}\n'
```

Outcome: the `{{root}}` injection point in the raw template is replaced by each mutation and sent to port 79 (finger), demonstrating raw-mode fuzzing of any line-based service without writing a protocol definition; suspects can be reviewed and re-sent from the console.

### Drive mutations from a custom wordlist

Replace the built-in mutation libraries with your own values.

```
python -m fuzzowski target.example.com 79 -f raw -r '{{root}}\n' --file my-fuzzlist
```

Outcome: the values in `my-fuzzlist` are used as the mutation set for the injection point instead of the predefined string libraries — the way to reuse project-specific payloads across targets.

### Target several requests of a stateful protocol

Fuzz specific requests of an implemented protocol in one run.

```
python -m fuzzowski target.example.com 515 -f lpd -r long_queue ctrl_file
```

Outcome: only the named LPD requests (`long_queue`, `ctrl_file`) are fuzzed rather than all of them (`-r` defaults to every request the fuzzer defines), keeping the run focused on the queue-handling paths of the line-printer daemon.
