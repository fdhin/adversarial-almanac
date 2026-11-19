---
name: "bss"
categories:
  - blackarch-bluetooth
version: "0.8"
homepage: "http://www.secuobs.com/news/15022006-bss_0_8.shtml"
packaged-by: [blackarch]
blackarch-builddate: "2023-06-30"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/bss"
github-stars: 1
github-pushed: "2019-10-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bss

> L2CAP fuzzer that smashes classic Bluetooth stacks with malformed signaling packets and generates replay programs for the crashes it finds.

## What it's for

bss (Bluetooth Stack Smasher, by Pierre Betouin) fires malformed L2CAP signaling packets at a Bluetooth device — random fuzzing, per-message-type fuzzing (CONN_REQ, CONF_REQ, ECHO_REQ and the rest), or full-header fuzzing — and detects crashes by watching whether the target stops answering l2pings. When a test case looks like it killed the device, bss can automatically emit a standalone replay program for that exact packet so the crash can be reproduced with `./replay <bdaddr>`. Use it to audit embedded and classic-Bluetooth stacks for L2CAP robustness; the attack runs over a normal ACL link, so the target only needs to be in range and accepting connections.

## Typical use cases

### Random-fuzz a target stack

Hammer the device with random L2CAP packets of a fixed size.

```
bss -M 0 -m 13 -s 10 00:11:22:33:44:55
```

Outcome: streams test cases (a dot per packet sent), prints `[!]` when the target stops responding to the secondary l2ping, and emits a "Potential crash detected" summary with the host and packet size; `-M 0` fuzzes without a crash-count limit and `-m 13` selects random L2CAP fuzzing.

### Fuzz quietly with automatic crash-replay generation

Long unattended run that records every suspected crash as a replayable testcase.

```
bss -q -o -x -M 0 -m 13 -s 10 00:11:22:33:44:55
```

Outcome: compact output where `.` means packet sent, `!` means suspected crash and `G` means a replay file was generated in `replay_packet/`; `-x` makes bss exit once a crash is confirmed by a failed l2ping, and each generated testcase is turned into a standalone replayer with `replay_packet/makereplay.sh` then replayed with `./replay <bdaddr>`.

### Fuzz one specific L2CAP message type

Target a single signaling command type instead of random fuzzing.

```
bss -m 4 -M 5 -s 64 00:11:22:33:44:55
```

Outcome: fuzzes only L2CAP_CONF_REQ packets (mode 4; modes 1-12 select the other signaling types, 12 full-header fuzzing, 0 runs everything) and stops after 5 detected crashes.

*No corpus evidence — derived from upstream documentation.*

*GitHub: `Seabreg/bss` is a third-party source mirror, byte-identical to the packaged 0.8 tarball (upstream is Pierre Betouin's secuobs.com release); stat fields predate this link and await recompute.*
