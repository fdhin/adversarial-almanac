---
name: "mptcp-abuse"
categories:
  - blackarch-networking
version: "6.b0eeb27"
homepage: "https://github.com/Neohapsis/mptcp-abuse"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Neohapsis/mptcp-abuse"
github-stars: 61
github-pushed: "2014-08-12"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mptcp-abuse

> Neohapsis toolkit from Black Hat USA 2014 for probing and abusing Multipath TCP (MPTCP) from ordinary, non-MPTCP hosts.

## What it's for

mptcp-abuse bundles the proof-of-concept tools from Neohapsis' 2014 Black Hat research ("Multipath TCP: Breaking Today's Networks with Tomorrow's Protocols"): a scanner that fingerprints which hosts and ports speak MPTCP, and a fragmenter that splits a single HTTP request across multiple MPTCP subflows to probe middlebox and server behavior. It runs from ordinary TCP-only hosts by crafting raw MPTCP packets with a bundled fork of scapy (MPTCP-capable, based on Nicolas Maitre's mptcp-scapy); both scripts need root for raw sockets and iptables management. The repo also ships the MPTCP cheatsheet PDF, the conference presentation, and a challenge pcap for testing against.

## Typical use cases

### Identify MPTCP-capable services on a host

```
sudo python2 mptcp_scanner.py 198.51.100.10 22,80
```

Outcome: for each port in the list the scanner runs MP_JOIN/RST probes and prints a verdict per port (e.g. `{22: 'MPTCP (MP_JOIN Verified)'}`), with the results summary listing exactly which services on the target accept MPTCP. Hosts and ports accept comma-separated lists and glob ranges.

### Split one HTTP request across multiple subflows

```
sudo python2 mptcp_fragmenter.py -n 5 -p 80 198.51.100.10
```

Outcome: opens the requested number of MPTCP subflows to the target (printing each source port as it connects), sends the payload fragmented across them (`--file` to substitute a custom payload, `--path` to change the requested URI), and reports each subflow closing — the shape used to test how middleboxes, load balancers and servers handle traffic reassembled from multiple paths.

*No corpus evidence — derived from upstream documentation.*
