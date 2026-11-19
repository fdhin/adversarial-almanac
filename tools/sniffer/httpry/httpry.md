---
name: "httpry"
categories:
  - blackarch-sniffer
version: "0.1.8"
homepage: "http://dumpsterventures.com/jason/httpry/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-01-31"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "jbittel/httpry"
github-stars: 600
github-pushed: "2018-04-21"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# httpry

> Lightweight libpcap sniffer for displaying and logging HTTP request and response headers (Jason Bittel).

## What it's for

httpry is a specialized packet sniffer for HTTP: instead of dumping raw payloads, it parses and displays the request/response line and associated header fields — method, host, user-agent, referrer, content-type and more, selectable via a format string. It can watch a live interface, run as a daemon that logs to a file, or process saved captures offline without privileges; extra modes write matched packets to a binary pcap and show per-host requests-per-second rates. It is deliberately an acquisition front-end, not an analyzer — upstream positions it as "capture, parse and/or log the traffic for later analysis", and the package ships Perl scripts for mining httpry log files. The parser is naive by design: no packet reordering or reassembly, and request lines fragmented across packets are truncated. BPF filter expressions narrow what is captured.

## Typical use cases

### Watch HTTP traffic live

```
sudo httpry -i eth0
```

Outcome: prints a live, one-line-per-transaction view of HTTP requests and responses on eth0 (timestamp, hosts, method, URI, status, headers per the default format string).

### Capture only specific methods on a port

```
sudo httpry -i eth0 -m GET,POST 'tcp dst port 80'
```

Outcome: parses only GET and POST requests among traffic to port 80 — the bpf-style expression replaces the default `tcp port 80 or 8080` filter.

### Run as a logging daemon

```
sudo httpry -d -i eth0 -o /var/log/httpry.log -P /var/run/httpry.pid
```

Outcome: detaches into the background (daemon mode requires `-o`), appends parsed lines to the log file, sends status to syslog, and writes the pid file.

### Extract HTTP data from a saved capture

```
httpry -r capture.pcap
```

Outcome: parses the pcap offline and prints the same output as a live run; reading a file needs no capture privileges.

### Monitor per-host request rates

```
sudo httpry -s -t 5 -i eth0
```

Outcome: rate-statistics mode prints every 5 seconds the requests-per-second for each active host plus the total (raise the display threshold with `-l`).

### Keep the raw packets behind what was parsed

```
sudo httpry -i eth0 -o parsed.log -b matched.pcap
```

Outcome: writes the parsed header lines to `parsed.log` and simultaneously saves every processed HTTP packet to the binary pcap `matched.pcap` for later inspection in tcpdump/Wireshark.

*No corpus evidence — derived from upstream documentation.*
