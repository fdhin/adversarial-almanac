---
name: "fastnetmon"
categories:
  - blackarch-defensive
version: "v1.2.9.r56.g2436f447"
homepage: "https://github.com/pavel-odintsov/fastnetmon"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: cmake
license: GPL
meta-checked: "2026-11-09"
github-repo: "pavel-odintsov/fastnetmon"
github-stars: 3726
github-pushed: "2026-11-02"
github-archived: false
github-issues: 60
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 100
gh-activity-checked: "2026-11-09"
---

# fastnetmon

> High-performance DoS/DDoS detector that watches whole networks via NetFlow/IPFIX/sFlow or port mirroring and triggers scripts, bans or BGP announcements when a host crosses traffic thresholds.

## What it's for

FastNetMon is a DDoS detection sensor: it tracks packets, bytes and flows per host across an entire network using flow telemetry (NetFlow v5/v9, IPFIX, sFlow) or packet capture on a mirror port (AF_PACKET, PCAP, AF_XDP), and flags a host as attacking or attacked within seconds of it crossing configured thresholds. What happens next is configurable: run a notify script, export to Kafka, Prometheus, ClickHouse or InfluxDB, or announce the offending IP via BGP (ExaBGP, GoBGP) so edge routers drop the traffic. It is a detector and trigger, not a scrubber — actual mitigation happens in the systems it calls. This is the open-source Community Edition of the commercial FastNetMon Advanced.

## Typical use cases

### Validate a configuration before restarting the sensor

Syntax-check thresholds and capture settings without touching running detection.

```
fastnetmon --configuration_check --configuration_file /etc/fastnetmon.conf
```

Outcome: parses the given file, reports any invalid keys or values, and exits without starting capture — safe to run from a config pipeline before a restart.

### Run the monitor in the foreground for a first look

See live per-host traffic and attack detection with nothing deployed.

```
sudo fastnetmon --log_to_console
```

Outcome: starts the capture backends configured in fastnetmon.conf, renders the live traffic/attack screen in the terminal and mirrors log messages to the console; Ctrl-C stops it.

### Deploy as a daemon

Keep detection running permanently on a sensor host.

```
sudo fastnetmon --daemonize --log_file /var/log/fastnetmon.log
```

Outcome: detaches from the terminal and monitors continuously; detection events are written to the given log file and pushed to the configured notify script.

### Tune what counts as an attack

Set the rates that flip a host into attack state.

```
# /etc/fastnetmon.conf
threshold_pps = 20000
threshold_mbps = 1000
threshold_flows = 3500
```

Outcome: with `ban_for_pps` / `ban_for_bandwidth` / `ban_for_flows` enabled, a host exceeding any of these rates is treated as an attack; networks in the file named by `white_list_path` are exempt, and `ban_time` controls how long a host stays banned.

### Hand attacks to your own tooling

Get called back on every detection.

```
# /etc/fastnetmon.conf
notify_script_path = /usr/local/bin/notify_about_attack.sh
```

Outcome: FastNetMon invokes that script when a host is flagged, so it can push a block to a firewall, page an operator or feed a SIEM.

*No corpus evidence — derived from upstream documentation.*
