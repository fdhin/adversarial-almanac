---
name: "darkstat"
categories:
  - sniffing-spoofing
version: "3.0.722-1"
homepage: "https://unix4lyfe.org/darkstat/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-10-22"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "emikulic/darkstat"
github-stars: 119
github-pushed: "2025-10-20"
github-archived: false
github-issues: 13
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# darkstat

> Packet-sniffing traffic accountant that serves real-time per-host, per-port usage graphs over its own web server.

## What it's for

darkstat (Emil Mikulic) is a network statistics gatherer: it passively captures packets on an interface, accumulates per-host and per-port traffic accounting in memory, and serves the results as a self-contained HTML report (usage graphs, host tables, port breakdowns) from its own built-in web server on port 667 by default. It is aimed at long-running, low-maintenance monitoring of a LAN segment or gateway — it daemonizes itself, can chroot and drop privileges, and can persist its database across restarts via import/export files. It deliberately does not inspect payloads: it answers "which hosts talk, to whom, on which ports, how much", not what the traffic contains.

## Typical use cases

### Start monitoring an interface

Turn a box into a traffic monitor with one command.

```
sudo darkstat -i eth0
```

Outcome: darkstat captures on eth0, daemonizes, and serves the live report at `http://localhost:667/` — graphs of traffic over time plus tables of hosts and their ports, updating as packets flow.

### Analyze a saved pcap offline

Answer "what was happening" from a capture taken earlier.

```
darkstat -r capture.pcap -p 8888 --no-dns
```

Outcome: instead of capturing live, darkstat reads the pcap file, builds the same host/port/graph report as a live run, and serves it on port 8888; `--no-dns` skips reverse DNS lookups so the report appears immediately and memory stays low.

### Scope accounting to a network and filter

Monitor only a defined local network or protocol.

```
sudo darkstat -i eth0 -l 198.51.100.0/255.255.255.0 -f "port 80" --local-only
```

Outcome: only traffic entering or leaving 198.51.100.0/24 is graphed (traffic to foreign hosts is still counted but the report focuses on the local network), further narrowed to port 80 by the pcap filter expression.

### Run persistently across restarts

Keep long-term statistics on a gateway.

```
sudo darkstat -i eth0 --chroot /var/darkstat --user nobody --daylog daylog.txt --export darkstat.db
```

Outcome: darkstat runs chrooted with dropped privileges, appends daily traffic totals to the daylog, and writes its whole in-memory database to darkstat.db on shutdown or SIGUSR1/SIGUSR2; restarting with `--import darkstat.db` resumes with the accumulated history intact (paths are relative to the chroot directory).

*No corpus evidence — derived from upstream documentation.*
