---
name: "filibuster"
categories:
  - blackarch-networking
version: "167.c54ac80"
homepage: "https://github.com/subinacls/Filibuster"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-01"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "subinacls/Filibuster"
github-stars: 27
github-pushed: "2017-07-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# filibuster

> Two-part egress-filter mapper: listen on the outside, probe from the inside, learn exactly what the firewall lets out.

## What it's for

Filibuster (by subinacls) verifies egress filtering between network
segments. One instance — the server — listens on an outside host; the
client runs inside the filtered network and systematically connects out to
it across a configured port range and protocol, reporting which ports and
protocols actually traverse the filter. The client also tests covert
channels (ICMP, NTP and DNS tunneling) and logs results to XML/JSON. It is
a Python 2 era tool whose upstream GitHub tree was later gutted by its
author; the BlackArch package restores functionality by shipping its own
`filterbuster.py` entrypoint, wrapped as the `filibuster` command shown
here.

## Typical use cases

### Start the outside listener

Stand up the receiving end on the unfiltered side.

```
filibuster server tcp 10000 no
```

Outcome: listens on TCP/10000 for client probes; the protocol argument
accepts `tcp`, `udp` or `both`, and the trailing `yes`/`no` enables a TLS
listener instead of plaintext.

### Sweep egress ports from inside the filtered network

Discover which outbound ports the firewall permits.

```
cat > efb.conf <<'EOF'
[SectionOne]
target: 198.51.100.10
range: 1-1024
random: True
protocol: TCP
consultant: user
location: lab

[SectionTwo]
sleep: static
nappy: 0
dwell: 0.01

[SectionThree]
suppress: no
logging: yes
logtype: xml
EOF
filibuster client ./efb.conf no
```

Outcome: the client walks the configured port range against the server at
198.51.100.10 (order randomized by `random: True`), prints which probes
egress successfully, tests the ICMP/NTP/DNS covert channels, and writes
the XML log of results for the run.

*No corpus evidence — derived from upstream documentation.*
