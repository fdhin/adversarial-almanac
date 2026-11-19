---
name: "snort"
categories:
  - blackarch-defensive
version: "2.9.20"
homepage: "http://www.snort.org/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2023-03-01"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "snort3/snort3"
github-stars: 3465
github-pushed: "2026-11-05"
github-archived: false
github-issues: 88
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 519
gh-activity-checked: "2026-11-09"
---

# snort

> Open-source network intrusion detection and prevention system that matches live traffic or captures against a ruleset.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Snort is the classic open-source IDS/IPS: it captures packets (via libpcap or a DAQ module), reassembles streams and matches them against a ruleset of signatures plus protocol-analysis preprocessors, alerting or (in inline mode) dropping on match. It has three run modes — packet sniffer, packet logger and NIDS — and is configured through `snort.conf` plus rule files; rules ship from snort.org (registered/paid feeds) or community sets like Emerging Threats. The 2.9.x series documented here is distributed as source tarballs from snort.org; Snort 3 (Snort++, the maintained rewrite on GitHub) moves to a Lua configuration and changes many command-line flags. Snort detects and blocks network traffic — it is not a log-analysis or host-monitoring tool.

## Typical use cases

### Validate the configuration and ruleset

Check a config edit before putting it in front of real traffic.

```
snort -T -c /etc/snort/snort.conf -i eth0
```

Outcome: Snort parses `snort.conf`, all included rules and preprocessors, then exits — on success it prints "Snort successfully validated the configuration!" and exits 0, making it usable as a restart gate in deployment scripts.

### Run as an NIDS on live traffic

Watch an interface with the full ruleset and see alerts in real time.

```
snort -A console -c /etc/snort/snort.conf -i eth0
```

Outcome: NIDS mode on eth0 — every matching rule prints a one-line alert on the console; use `-A fast` or `-A full` for file-based alert output instead, add `-D` to daemonize, and `-l /var/log/snort` to set the log directory.

### Replay a capture through the ruleset

Test rule changes safely against recorded traffic.

```
snort -q -A console -c /etc/snort/snort.conf -r capture.pcap
```

Outcome: Snort reads the pcap instead of a live interface and prints every rule that fires on it (`-q` suppresses the banner and statistics) — a dry run for new or tuned rules before deployment.

### Batch a directory of captures

Process many pcaps in one invocation.

```
snort -q -A console -c /etc/snort/snort.conf --pcap-dir pcaps/ --pcap-filter '*.pcap' --pcap-show
```

Outcome: iterates every matching capture under `pcaps/`, printing which file is being read (`--pcap-show`) followed by its alerts, with the ruleset reloaded between files when combined with `--pcap-reload`.

### Sniff and log packets without rules

Use it as a plain diagnostic sniffer/logger.

```
snort -dev -l /var/log/snort
```

Outcome: packet-logger mode — dumps link-layer (`-e`), network and application-layer (`-d`) headers and payloads for observed traffic into the log directory, a tcpdump-style sanity check of what the sensor actually sees before any rules are loaded.
