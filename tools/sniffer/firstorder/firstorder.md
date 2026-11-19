---
name: "firstorder"
categories:
  - blackarch-sniffer
version: "8.107eb6a"
homepage: "https://github.com/tearsecurity/firstorder"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:BSD
meta-checked: "2026-11-09"
github-repo: "tearsecurity/firstorder"
github-stars: 107
github-pushed: "2018-08-13"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# firstorder

> Profile normal traffic from a pcap and auto-configure an Empire C2 listener that blends into it.

## What it's for

firstorder (Utku Sen, TEAR Security) is a red-team aid for evading anomaly-based network intrusion detection. Anomaly-based NIDS model "normal" traffic and flag statistical outliers, so a C2 channel whose port, URI, user-agent or server header deviates from the site norm gets caught. firstorder analyzes a packet capture of the target network's routine traffic and reports the most-used ports, server headers, user-agents and request URIs, plus how many distinct machines sent ARP broadcasts or LDAP queries (a network-size estimate). Given access to an Empire instance running in REST API mode, it then creates a listener whose options mirror that extracted normal profile, so agent-to-C2 communication does not stand out. The pcap-analysis mode works standalone; listener creation requires Empire (Python 2.7 era tooling, scapy + requests). It reads existing captures — it does not sniff live interfaces itself.

## Typical use cases

### Profile the normal traffic of a network

```
firstorder -f capture.pcap
```

Outcome: prints top-10 statistics for ports, server headers, user-agents and URIs observed in the capture, plus ARP/LDAP-based network size hints. No listener is created and no connection to Empire is made.

### Deploy a profile-matched Empire listener

Start Empire with its REST API enabled (e.g. `empire --rest --username empireadmin --password Password1`), then:

```
firstorder -f capture.pcap -u empireadmin -p Password1
```

Outcome: analyzes the capture and, via Empire's REST API, automatically creates a listener named "firstorder" whose port, URI, user-agent and server header match the most common values seen in normal traffic — the beaconing then resembles ordinary site traffic to an anomaly detector.

*No corpus evidence — derived from upstream documentation.*
