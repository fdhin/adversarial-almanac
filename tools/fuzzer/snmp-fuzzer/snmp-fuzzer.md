---
name: "snmp-fuzzer"
categories:
  - blackarch-fuzzer
version: "0.1.1"
homepage: "http://www.arhont.com/en/category/resources/tools-utilities/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# snmp-fuzzer

> Perl SNMP fuzzer that replays the PROTOS test suite against SNMP agents and identifies the packet that crashed them.

*No corpus evidence — derived from upstream documentation.*

## What it's for

snmpfuzz (Arhont) sends the University of Oulu PROTOS SNMP test cases — SNMPv1 requests, BER-encoded requests, traps and encoded traps — to an SNMP agent and, unlike plain Protos, tries to tell you which packet killed the service: it sniffs ICMP port-unreachable responses (the offending SNMP PDU is quoted inside them) and periodically sends "SNMP pings" (SysDescr get requests with community `public`) to detect a dead agent, optionally aborting the run on first failure (`--kill`). Test cases can be replayed one at a time (`--file`), sequentially in a single process, or forked across several workers (`--fork`, `--maxprocess`). It cannot distinguish a crashed SNMP service from a crashed host on its own — run a parallel ICMP ping and compare. Written in Perl; the packaged `snmp-fuzzer` wrapper runs the engine from a share directory that also holds the test-case corpus.

## Typical use cases

### Fuzz an agent with the full request battery

Replays all SNMPv1 request test cases with parallel workers and liveness checks.

```
snmp-fuzzer --snmp --nodos --ping --fork --maxprocess 10 --host 198.51.100.10
```

Outcome: forks ten workers plus an ICMP listener and sends the Protos request cases to 198.51.100.10:161, tracking liveness with SNMP pings; an ICMP port-unreachable line or a missing pong identifies the failing case number, which you then confirm with a single-packet replay.

### Replay one test case

Sends exactly one captured PDU to isolate a crash.

```
snmp-fuzzer --file /usr/share/snmp-fuzzer/snmp/00000001 --host 198.51.100.10
```

Outcome: transmits only that numbered test-case PDU to the target so you can observe the agent's reply, timeout or crash in isolation.

### Fuzz a trap listener

Aims the trap test cases at an SNMP manager instead of an agent.

```
snmp-fuzzer --trap --ping --host 198.51.100.10 --port 162
```

Outcome: sends trap PDUs to the manager on UDP 162 and uses missing ping replies to flag the case number that took the listener down.
