---
name: "spike-fuzzer"
categories:
  - blackarch-fuzzer
version: "2.9"
homepage: "http://www.immunitysec.com/resources-freesoftware.shtml"
packaged-by: [blackarch]
blackarch-builddate: "2019-03-29"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "SofianeHamlaoui/Spike-Fuzzer"
github-stars: 11
github-pushed: "2019-07-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spike-fuzzer

> SPIKE 2.9 — Immunity's block-based fuzzer creation kit in C, driven by generic_send_tcp and sibling binaries.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SPIKE (Dave Aitel, Immunity) is a fuzzer creation kit rather than a ready-made fuzzer: a protocol exchange is described in a small C script (`.spk`) using the SPIKE API — `s_string`, `s_binary`, `s_int_variable`, block delimiters with size words such as `s_binary_block_size_halfword_bigendian_variable` — and generic driver binaries iterate mutations over every marked element while keeping length fields consistent. Shipped drivers cover TCP and UDP (`generic_send_tcp`, `generic_send_udp`), web servers (`generic_web_server_fuzz` and `generic_web_server_fuzz2`), a listening mode (`generic_listen_tcp`), and specific protocols (msrpcfuzz, sunrpcfuzz, x11_spike, webfuzz, statd_spike, ...). Effective use requires C and protocol knowledge; the upstream `audits/` directory is a library of example `.spk` scripts for protocols from SMTP to CIFS. On BlackArch the binaries are prefixed `spike-fuzzer-`, and an optional Python/wxWidgets GUI console (SPIKE_Console.py) ships with the source. Not to be confused with [[spike]], the RISC-V ISA simulator — nor with Kali's `spike` package, which ships this same fuzzer under the bare name `spike`.

## Typical use cases

### Fuzz a TCP service with a SPIKE script

Runs a `.spk` protocol script against a live listener.

```
spike-fuzzer-generic-send_tcp 198.51.100.10 445 cifs1.spk 0 0
```

Outcome: repeatedly connects to 198.51.100.10:445, replaying the script with the next fuzzable element mutated on each pass while logging traffic; when the target stops responding, the last request printed is the prime suspect — rerun with a higher first trailing integer to resume past the already-cleared prefix.
