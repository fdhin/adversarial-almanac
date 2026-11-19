---
name: "maltrail"
categories:
  - blackarch-defensive
version: "3.5.r89.g68c5474"
homepage: "https://github.com/stamparm/maltrail"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "stamparm/maltrail"
github-stars: 8653
github-pushed: "2026-11-07"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 781
gh-activity-checked: "2026-11-09"
---

# maltrail

> Passive sensors that flag traffic to known-bad domains, IPs and TLS fingerprints, with a web console for triage.

## What it's for

Maltrail is a malicious-traffic detection system built from two independently deployable processes. The sensor passively captures network traffic and matches observed DNS lookups, URLs, IP addresses, IP:port pairs, User-Agents and TLS client/server fingerprints against indicators called trails, on top of heuristic detections for scanning, DGA-like DNS, suspicious downloads and proxy probes. Trails merge a large curated static set with dozens of public feeds and your own custom indicators, refreshed automatically. The Python server stores events and serves a browser interface (default http://127.0.0.1:8338) with live updates, field-aware search, retro-hunt across retained logs, geo views and per-threat triage; sensors can also forward events to a central maltrail server, CEF syslog or Logstash. It is detection and monitoring only — not an intrusion prevention system. The shipped configuration has a default admin login accepted only from the server itself, so set your own USERS entry and put TLS in front before exposing the interface. Older releases shipped a Python sensor; in current releases the sensor is a Rust binary (`maltrail-sensor`) while the server stays Python (`python3 server.py`, packaged as `maltrail-server`).

## Typical use cases

### Validate and start a sensor plus server

```
maltrail-sensor -T
maltrail-sensor
maltrail-server
```

Outcome: `-T` is the deployment gate — it validates configuration, capture privileges, the trail set and whitelist (with positive counts) and fails visibly on problems, which is why the supplied systemd unit runs it as ExecStartPre; the sensor then captures on MONITOR_INTERFACE (set in maltrail.conf) and writes events to LOG_DIR, while the server serves the reporting interface at HTTP_ADDRESS:HTTP_PORT for login.

### Query one indicator against the trail database

```
curl 'http://127.0.0.1:8338/check?q=www.sub.evil.example'
```

Outcome: returns JSON with `found`, the matched trail, its classification, the reference source and a 0-100 confidence score reflecting how many independent feeds back the listing; a subdomain can match its listed parent. Public static and feed trails answer without authentication; custom trails require a logged-in session.

### Prove detection works end to end

```
python3 server.py --detect-test
```

Outcome: replays a crafted pcap of emulated malicious traffic (trail hits on DNS, IP, IP:port, URL path and Host header, plus scanning, SQL-injection, traversal and proxy-probe heuristics) through the installed sensor and asserts every expected detection fires — a healthy install prints `27/27 detection(s) fired`, needing no root, capture interface or separate trail set.

### Reload trails without dropping the sensor

```
systemctl reload maltrail-sensor
```

Outcome: sends SIGHUP, requesting a reload of the trail database without restarting capture; trail files updated by another process are also detected and published to the capture workers automatically.
