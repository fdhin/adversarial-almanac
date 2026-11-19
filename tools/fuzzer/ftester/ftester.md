---
name: "ftester"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "http://www.inversepath.com/ftester.html"
packaged-by: [blackarch, kali]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "f-secure-foundry/ftester"
github-stars: 24
github-pushed: "2017-08-02"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ftester

> Inverse Path's Firewall Tester: `ftest` injects signature-marked packets, `ftestd` sniffs them on the far side of the firewall, and `freport` diffs the two logs to reconstruct the effective filtering policy.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ftester (by Andrea Barisani, Inverse Path, 2001–2006; the upstream archive states the tool is outdated and kept for historical reasons) tests firewall filtering policies and, from version 0.6, IDS coverage with realistic traffic. Two hosts cooperate: ftest outside the firewall generates the packets defined in ftest.conf, each marked by its IP ID; ftestd inside sniffs and logs what actually arrived; freport compares the logs and classifies each packet as authorized, modified (probably NAT) or filtered/dropped. Because the sniffer recognizes its own marked packets, source addresses can be spoofed and stateful-inspection firewalls exercised with TTL-based "connection spoofing"; ftest also carries evasion options (IP fragmentation, TCP segmentation, bogus checksums). Perl scripts needing Net::RawIP, Net::PcapUtils and NetPacket.

## Typical use cases

### Start the inside sniffer

Listen for marked packets on the internal side of the firewall.

```
ftestd -i eth0 -v
```

Outcome: ftestd listens on eth0, prints each marked packet it observes and writes ftestd.log; it keeps running until it sees the stop_signal packet configured in ftest.conf.

### Inject the test policy from outside

Play every packet defined in the configuration file.

```
ftest -f ftest.conf -v -d 0.01
```

Outcome: ftest sends each packet defined in ftest.conf — lines of the form `198.51.100.10:1025:198.51.100.20:22:S:TCP:0` (source:port:destination:port:flags:protocol:TOS), with ranges and CIDR blocks allowed, plus a final `stop_signal=...` line — paced 10 ms apart (`-d`); every sent packet is printed and logged to ftest.log.

### Fire a single ad-hoc probe

Check the path through the firewall with one packet and no config file.

```
ftest -c 198.51.100.10:1025:198.51.100.20:80:S:TCP:0 -v
```

Outcome: sends a single SYN probe from 198.51.100.10:1025 to 198.51.100.20:80 — handy to verify that generator and sniffer can see each other through the firewall before running a full policy test.

### Exercise fragmentation handling

Send the same policy as IP fragments.

```
ftest -f ftest.conf -v -g 4
```

Outcome: each test packet is sent as 4 IP fragments (`-g`), showing whether the firewall and IDS reassemble fragments before filtering — traffic that passes whole but vanishes fragmented exposes a fragment-handling weakness.

### Reconstruct the effective policy

Compare what was sent with what arrived.

```
freport ftest.log ftestd.log
```

Outcome: with both logs copied to one host, freport prints the packets grouped into Authorized packets, Modified packets (probably NAT, shown with the before/after tuple) and Filtered or dropped packets — the firewall's effective ACL as observed end-to-end.
