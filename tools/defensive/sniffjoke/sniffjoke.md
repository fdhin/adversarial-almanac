---
name: "sniffjoke"
categories:
  - blackarch-defensive
version: "772.434bfb1"
homepage: "http://www.delirandom.net/sniffjoke/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2019-01-28"
build-system: cmake
license: GPL
meta-checked: "2026-11-09"
github-repo: "vecna/sniffjoke"
github-stars: 113
github-pushed: "2017-08-25"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sniffjoke

> Transparently scrambles your outgoing TCP streams so passive sniffers and IDS cannot reliably reassemble them.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SniffJoke (by vecna, s0ftpj) is a Linux anti-sniffing tool: it routes local TCP connections through a tun device and then delays, re-segments, reorders and injects decoy packets into each stream so that a passive observer on the path — a sniffer, IDS or interception middlebox — reassembles something different from what the legitimate endpoints see. The legitimate connection stays intact because the manipulation exploits differences between OS TCP/IP stacks. It is location-aware: every network path tolerates different manipulations, so you first profile a location with `sniffjoke-autotest` and then run the service for that profile. Requirements are Linux with tun support and a wifi/eth default gateway. The upstream project declares itself abandonware (last release 0.4.1, 2017; the delirandom.net documentation domain is expiring — use a web archive copy of the man pages), so treat it as a research/evasion tool rather than production privacy tooling. Its own logs and configs live under a per-location directory with plugins (`fake_close_fin`, `fragmentation`, `segmentation`) you enable per profile.

## Typical use cases

### Profile the current network first

Measure which TCP manipulations this path tolerates before scrambling anything.

```
sniffjoke-autotest -l office
```

Outcome: runs the probe suite against the network you are on and writes the resulting `office` location configuration (iptables/TCP options, enabled plugins, TTL focus map); repeat per location — home, office, lab — since each profile is only valid for its network.

### Start the scrambler for a location

Run the service that mangles matching TCP flows.

```
sniffjoke --location office
```

Outcome: the sniffjoke service starts with the `office` profile, creates its tun device and applies the enabled plugins to matching outgoing connections; run it under a supervisor or the provided service script for long-term use.

### Control the running service and check statistics

Drive the daemon from its client.

```
sniffjokectl --start
sniffjokectl --stat
```

Outcome: `--start` activates scrambling on the running service and `--stat` prints per-plugin counters (injected, delayed, segmented packets), confirming traffic is actually being processed; `sniffjokectl --help` lists the remaining control commands.
