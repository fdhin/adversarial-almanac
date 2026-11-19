---
name: "junkie"
categories:
  - blackarch-sniffer
version: "1365.70a83d6"
homepage: "https://github.com/krissg/junkie"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2015-07-28"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "krissg/junkie"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 2
github-pushed: "2010-12-31"
github-archived: false
github-issues: 0
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# junkie

> Modular real-time packet sniffer and protocol analyzer positioned between tcpdump and Wireshark.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Parsing live traffic at protocol depth while it flows, rather than after the fact. Junkie is the capture and analysis core of SecurActive's network-performance monitoring product, released as open source: a threaded libpcap capture engine that keeps up with high-bandwidth links, a plugin system for per-protocol dissectors, and Guile Scheme as the configuration and extension language. By itself it does nothing with the parsed packets — you must load a plugin (e.g. the bundled dumper) to display or measure anything. Compared to tcpdump it decodes further into protocol layers; compared to Wireshark it trades completeness for real-time speed. The original SecurActive GitHub organization is gone; the packaged source builds from the lead author's repository, and the binary depends on an old OpenSSL, so treat it as legacy.

## Typical use cases

### Watch decoded traffic live with the dumper plugin

```
junkie -i eth0 -p dumper.so
```

Outcome: junkie captures on eth0, loads the dumper plugin, and prints everything it decodes from the live stream — per-protocol breakdowns of the traffic as it is analyzed; stop with Ctrl-C.

### Analyze an existing pcap file

```
junkie -r capture.pcap -p dumper.so
```

Outcome: packets are read from the pcap file instead of an interface and pushed through the same plugin pipeline, printing the decoded protocol summary offline.

### Run as a background daemon with logging

```
junkie -i eth0 -b -l /var/log/junkie.log -p dumper.so
```

Outcome: the sniffer detaches into the background and writes its log to the given file, leaving the terminal free; useful for leaving a long-running capture-analysis session on a monitor host.

### Expose metrics as an SNMP subagent

```
junkie -i eth0 -c /etc/junkie/snmp-subagt.scm
```

Outcome: junkie loads the SNMP subagent configuration and reports its measurements (the shipped JUNKIE-MIB) to a local agentX SNMP daemon, so network statistics become pollable like any other SNMP metric.
