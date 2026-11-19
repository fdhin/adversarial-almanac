---
name: "zeek"
categories:
  - blackarch-networking
version: "v9.1.0.dev.r292.g26380f2"
homepage: "https://github.com/zeek/zeek"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: node
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "zeek/zeek"
github-stars: 8185
github-pushed: "2026-11-06"
github-archived: false
github-issues: 273
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2397
gh-activity-checked: "2026-11-09"
---

# zeek

> Zeek (formerly Bro): passive network analysis framework that turns traffic into rich, structured logs, scriptable in its own language.

## What it's for

Zeek is a powerful network analysis framework that is much different from the typical IDS: rather than matching signatures, it parses traffic into semantically rich, structured logs — conn.log for every connection, http.log, dns.log, ssl.log, and dozens more — while keeping extensive application-layer state. Its domain-specific scripting language lets you hook any protocol event and express site-specific detection and analysis policies, which makes it a framework for network traffic analysis generally, not a fixed-purpose scanner. Use it interactively for "what happened in this capture?" forensics (`zeek` on a pcap or interface) or as a continuously deployed monitor managed by the bundled ZeekControl (`zeekctl`). It is passive by design — analysis, not inline blocking (the RST utility in [[zeek-aux]] can sever a flow a script selects).

## Typical use cases

### Analyze a pcap with default logging

Get Zeek's view of a capture with one command.

```
zeek -r capture.pcap
```

Outcome: Silently writes Zeek's default logs into the current directory — conn.log, http.log, weird.log and friends — one entry per connection or protocol event observed in the trace; inspecting conn.log is the fastest inventory of who talked to whom.

### Emit JSON logs

Make the logs machine-readable.

```
zeek -r capture.pcap LogAscii::use_json=T
```

Outcome: The same analysis and log files, but every record is a JSON object instead of Zeek's ASCII column format — ready for jq, Logstash, or any JSON consumer.

### Run a custom script over traffic

Apply your own policy to a capture.

```
zeek http-requests.zeek -r capture.pcap
```

Outcome: Executes the script's event handlers against the trace — e.g. a script printing one line per HTTP request it sees (`event http_request(c, ...)`) with method, URL and both endpoints.

### Monitor live traffic

Analyze an interface in real time.

```
zeek -i eth0 -C
```

Outcome: Zeek attaches to eth0 and writes the same logs to the current directory as traffic arrives (root is typically required for capture access) — a minimal ad-hoc sensor; larger deployments go through zeekctl instead.

### Deploy a managed sensor with ZeekControl

Run Zeek as a persistent, configured monitor.

```
zeekctl
```

Outcome: Opens the ZeekControl console; `deploy` starts the node defined in $PREFIX/etc/node.cfg (set its `interface=` to your capture device), `top` shows node status, and `stop` halts monitoring. Site policy belongs in $PREFIX/share/zeek/site/local.zeek — loaded by default by zeekctl and preserved across upgrades — and running logs land in $PREFIX/logs/current.

### Scale live capture with AF_PACKET fanout

Spread one high-volume interface across worker processes on Linux.

```
# /opt/zeek/etc/node.cfg — worker stanza
[worker]
type=worker
host=198.51.100.20
interface=af_packet::eth0
lb_method=custom
lb_procs=4
```

Outcome: After `zeekctl deploy` (or `zeekctl install && zeekctl start`), the af_packet packet source fans eth0's packets across 4 load-balanced Zeek workers (per-flow hashing) without mirror ports or capture hardware; tuning options like af_packet_block_size, af_packet_fanout_mode and af_packet_buffer_size adjust the kernel ring behavior.

*No corpus evidence — derived from upstream documentation.*
