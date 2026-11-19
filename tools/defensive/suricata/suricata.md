---
name: "suricata"
categories:
  - blackarch-defensive
version: "6.0.20"
homepage: "https://suricata.io/download/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-06-30"
build-system: rust
license: GPL2
meta-checked: "2026-11-09"
github-repo: "OISF/suricata"
github-stars: 6834
github-pushed: "2026-11-07"
github-archived: false
github-issues: 89
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1654
gh-activity-checked: "2026-11-09"
---

# suricata

> Open-source IDS/IPS engine that inspects live traffic or replayed pcaps against signature rules and protocol analysis, writing structured alerts to eve.json and fast.log.

## What it's for

Suricata (OISF) is a next-generation intrusion detection and prevention engine: it captures packets (live NIC, NFQUEUE for inline IPS, or pcap files), matches them against Suricata/Snort-compatible rule sets plus protocol analysis, and emits rich JSON telemetry in `eve.json` — alerts, flows, DNS, HTTP, TLS and file transactions — alongside the human-readable `fast.log` and periodic `stats.log`. Detection engineers use it to test rule and config changes safely (config test mode, pcap replay) and to run sensors; responders mostly consume its logs under `/var/log/suricata` and its rules under `/etc/suricata/rules`. It inspects network traffic — it is not a host-based detection product.

## Typical use cases

### Validate the configuration before (re)starting the engine

Catch YAML and rule errors without touching a running sensor.

```
suricata -T -c /etc/suricata/suricata.yaml -v
```

Outcome: Suricata loads the configuration and full ruleset in test mode and exits without capturing; a successful test completes with no reported errors, while a broken config or rule pinpoints the exact file and problem. Each extra `-v` raises logging verbosity (INFO, PERF, CONFIG, DEBUG) to see more of what was loaded.

### Replay a pcap through the ruleset

Re-run captured traffic offline to validate new rules or investigate an incident.

```
suricata -r traffic.pcap
```

Outcome: Suricata processes the pcap in offline mode and writes alerts and metadata to the log directory exactly as if the traffic had been seen live (`/var/log/suricata/eve.json`, `fast.log`, `stats.log`). Passing a directory instead of a file replays every pcap in it in modification order, keeping flow state between files.

### Start live capture on an interface

Run Suricata as a live sensor on network traffic.

```
suricata -i eth0
```

Outcome: Suricata attaches to the interface with the best available capture method and begins inspecting traffic, logging alerts to `/var/log/suricata`. `-i` may be given several times to sniff multiple interfaces, and on Linux `--af-packet` forces AF_PACKET capture.

### Control a running instance over its Unix socket

Manage a live sensor — reload rules, read counters, shut down — without signals.

```
suricatasc -c version
suricatasc -c "iface-stat eth0"
suricatasc -c ruleset-reload-rules
```

Outcome: each command contacts the running engine over its control socket and prints a one-line JSON reply such as `{'message': '7.0.5 RELEASE', 'return': 'OK'}` — here the engine version, per-interface packet/drop counters, and a synchronous ruleset reload. `shutdown` stops the engine; multi-word commands must be quoted as shown.

### Triage alerts out of eve.json

Pull just the alert records from the JSON event stream during analysis.

```
jq -c 'select(.event_type=="alert")' /var/log/suricata/eve.json
```

Outcome: one compact JSON alert object per line (signature, source and destination IPs/ports, flow ID) with all non-alert telemetry filtered out — ready to pipe into further `jq` queries or `sort | uniq -c` to rank firing signatures.

### Hunt through rotated, compressed logs

Search alert history when the activity of interest may be hours or days old.

```
zgrep -l 'login' /var/log/suricata/eve.json.*.gz
```

Outcome: prints the rotated `eve.json` archives containing the keyword, so analysis continues with `zcat <archive> | jq ...` on just those files instead of decompressing everything; the same works over `fast.log.*.gz` and `stats.log.*.gz`, with plain `grep` covering the current uncompressed logs.

### Review the rules loaded on a sensor

See which signatures the engine is actually enforcing before editing them.

```
grep -n 'ftp' /etc/suricata/rules/*.rules
```

Outcome: every rule line mentioning the keyword, with file and line number, across the active ruleset directory — useful for locating a SID or confirming which stock per-protocol files (e.g. `ftp-events.rules`) are deployed on the sensor.
