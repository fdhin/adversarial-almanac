---
name: "arpspoof-smikims"
categories:
  - blackarch-spoof
version: "31.ef5f0b1"
homepage: "https://github.com/smikims/arpspoof"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-13"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "smikims/arpspoof"
github-stars: 195
github-pushed: "2024-11-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arpspoof-smikims

> Minimal C ARP-spoofing tool (smikims) that convinces a single victim the attacker is the default gateway, using raw sockets and optional periodic re-sending.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Enabling a man-in-the-middle position against one host on an unencrypted local network. The program first ARPs the gateway and the victim to learn their MACs, then sends the victim a forged ARP response claiming the attacker's MAC belongs to the gateway IP. That is all it does — it forwards nothing, sniffs nothing and injects nothing; pairing it with a capture tool or an iptables redirect is up to you (the upstream README sketches a Squid/Apache setup for that). Distinguish it from dsniff's arpspoof: this one targets a single victim rather than poisoning a pair continuously, and Linux victims tend to detect the ruse quickly, so the `-r` repeat option matters. The package also ships `sweep.sh` (ping-sweep the subnet, then spoof everyone who answers) and `cage.sh` (a Squid URL rewriter replacing every page with Nicolas Cage); on BlackArch the binaries install as `arpspoof-smikims`, `arpspoof-smikims-sweep` and `arpspoof-smikims-cage`.

## Typical use cases

### Impersonate the gateway to one victim

One sentence of intent: put yourself between one host and its router.

```
sudo arpspoof-smikims -i eth0 198.51.100.10
```

Outcome: sends one forged ARP response telling `198.51.100.10` (the victim) that the attacker is the default gateway; the victim's traffic now transits the attacker's interface. If nothing happens within about a second the packet was likely lost — run it again or use `-r`.

### Keep the poisoning alive

One sentence of intent: stop the victim's OS from re-verifying its ARP cache.

```
sudo arpspoof-smikims -i eth0 -r 10 198.51.100.10
```

Outcome: re-sends the phony response every 10 seconds so the cache entry never expires; `-v` adds details about the machines involved.

### Impersonate a host other than the gateway

One sentence of intent: redirect the victim's traffic aimed at an arbitrary IP, or impersonate a different attacker box.

```
sudo arpspoof-smikims -a 198.51.100.20 -g 198.51.100.2 198.51.100.10
```

Outcome: `-g` spoofs an IP other than the default gateway (here `.2`), and `-a` claims the MAC of another machine (`.20`) instead of the one running the program — useful when the spoofing box must stay invisible.

### Sweep a subnet for live victims

One sentence of intent: discover who is on the segment before (or while) attacking.

```
sudo arpspoof-smikims-sweep eth0 -p
```

Outcome: ping-sweeps the local subnet and prints the IPs that respond (the `-p` flag stops after the sweep — drop it to also fire spoof packets at every responder).
