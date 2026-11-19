---
name: "hyde"
categories:
  - blackarch-networking
version: "11.ec09462"
homepage: "https://github.com/CoolerVoid/Hyde"
packaged-by: [blackarch]
blackarch-builddate: "2017-01-12"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "CoolerVoid/Hyde"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# hyde

> Minimal C tool that floods a target with crafted TCP packets using spoofed or mirror-spoofed source addresses.

## What it's for

hyde (Cooler_ / BugSec Security TEAM, 2010) is a compact C stress-testing tool that pushes a chosen volume of crafted TCP packets at one host:port from multiple threads. Nine flood types combine flag sets — XMAS, SYN+ACK, FIN+ACK, URG+ACK, PSH+ACK — with either plain source spoofing or "mirror" spoofing that presents the target's own address as the source. It is a deliberate DDoS-style load generator for testing how network equipment and services behave under hostile packet patterns, and its author's warning stands: use it only against systems you are authorized to test. The upstream repository is gone; the interface below is preserved from its README as archived.

## Typical use cases

### Launch a crafted TCP flood

```
sudo hyde target.example.com 80 20000 4 5
```

Outcome: sends 20000 type-4 packets (FIN+ACK with mirror spoofing) at port 80 of target.example.com using 5 threads; the tool itself prints little, so watch the effect in a traffic counter or on the target itself. The fourth argument selects the pattern: 0 XMAS, 1 SYN+ACK, 2 SYN+ACK mirror, 3 FIN+ACK, 4 FIN+ACK mirror, 5 URG+ACK, 6 URG+ACK mirror, 7 PSH+ACK, 8 XMAS mirror.

*No corpus evidence — derived from upstream documentation.*
