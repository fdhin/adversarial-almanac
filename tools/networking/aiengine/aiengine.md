---
name: "aiengine"
categories:
  - blackarch-networking
version: "2.0.1"
homepage: "https://bitbucket.org/camp0/aiengine/downloads/"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-26"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "camp0/aiengine"
github-stars: 31
github-pushed: "2017-05-05"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aiengine

> Programmable packet-inspection engine with regex matching, frequency learning and Yara generation over live or captured traffic.

## What it's for

AIEngine (by camp0) is a C++ network traffic analysis engine with Python/Ruby/Java/Lua bindings. It parses traffic into flows across a long protocol list (HTTP, DNS, SSL, SMTP, Bitcoin, Modbus, VXLAN and many more), evaluates regexes against those flows, runs a frequency/learner engine that characterizes unknown traffic and auto-generates regexes, can export Yara signatures, and can even reject matching TCP/UDP flows — giving it NIDS functionality. Input is a network interface, a pcap file, or a directory of pcaps. It is an embeddable engine for building classifiers, IDS signatures and forensics tooling, not a finished IDS product with alert rules out of the box.

## Typical use cases

### Inspect live traffic with protocol statistics

```
sudo aiengine -I eth0 -s 5
```

Outcome: live statistics of the network stack up to verbosity level 5, showing per-protocol flow counts as traffic is parsed — useful to confirm the stack type (`-n lan|lan6|mobile|virtual|oflow`) sees what you expect.

### Match flows against a regex on a capture

```
aiengine -I capture.pcap -R -r '(?i)user-agent: curl' -m
```

Outcome: enables the signature engine (`-R`), evaluates the regex (`-r`) against TCP/UDP flows and prints only the matching flows (`-m`); the regex defaults to a catch-all when `-r` is omitted.

### Keep forensic evidence of matches

```
aiengine -I capture.pcap -R -r 'failed login' -m -w
```

Outcome: in addition to printing the matched flows, `-w` writes a pcap file containing exactly the matching packets for later forensic analysis.

### Learn unknown traffic and generate a Yara signature

```
aiengine -I capture.pcap -F -f tcp -g dst-port -L -y
```

Outcome: enables the frequency engine grouped by destination port (`-g`) with the learner (`-L`) active, characterizing unknown traffic and emitting a Yara signature derived from it (`-y`) that can be reused in NIDS deployments.

*No corpus evidence — derived from upstream documentation.*
