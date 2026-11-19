---
name: "mots"
categories:
  - blackarch-sniffer
version: "5.34017ca"
homepage: "https://github.com/kevinkoo001/MotS"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "kevinkoo001/MotS"
github-stars: 8
github-pushed: "2015-10-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mots

> Man-on-the-Side attack toolkit: one command injects spoofed responses, its twin detects them.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Experimenting with Man-on-the-Side (MotS) injection — the on-path attack where a censor races the real server's reply with a forged one, as opposed to man-in-the-middle which stays in the flow. The package ships two complementary Python 2/scapy tools: `quantuminject` watches BPF-filtered traffic, matches request packets against a regular expression, and fires a forged TCP response carrying a payload file you supply; `quantumdetect` looks for the fingerprints of such injection — duplicated/suspicious packets — either live on an interface or offline against a pcap. Both write annotated logs keyed by IP IDs so individual injected packets can be traced. Research code from 2015; use it on networks you control.

## Typical use cases

### Inject a spoofed response to matched requests

```
sudo quantuminject -i eth0 -r 'w?rks' -d test.dat -b 'dst 198.51.100.10'
```

Outcome: monitors the BPF-filtered subset of eth0 traffic; for every request packet whose payload matches the regex, a forged response carrying test.dat as TCP payload is injected back. The terminal and `injection.log` record each forged packet's IP ID alongside the original packet's, e.g. "2 packets are injected! (20 monitored)".

### Detect injection in a saved capture

```
quantumdetect -r capture.pcap -b 'dst 198.51.100.10'
```

Outcome: offline scan of the pcap for suspicious duplicated packets; a summary count is printed (e.g. "5 packets are detected as injection! (200 inspected)") and each suspected pair's IP IDs is appended to `detection.log`.

### Monitor live for on-path injection

```
sudo quantumdetect -i eth0 -b tcp
```

Outcome: live capture on eth0 limited by the BPF filter; suspected injected packets are reported to the terminal and `detection.log` as they occur, letting you watch an active MotS attack in real time.
