---
name: "fping"
categories:
  - information-gathering
version: "5.5-1"
homepage: "https://www.fping.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-01-01"
build-system: autotools
license: custom
meta-checked: "2026-11-09"
github-repo: "schweikert/fping"
github-stars: 1237
github-pushed: "2026-11-06"
github-archived: false
github-issues: 13
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 61
gh-activity-checked: "2026-11-09"
---

# fping

> The classic (1992) high-throughput ping: probe hundreds or thousands of hosts in parallel from the command line, a file, or a generated address range.

## What it's for

fping (originally by Roland Schemers, maintained by David Schweikert) sends ICMP echo probes like ping, but is built for many targets at once: it round-robins through a host list instead of waiting for each reply, so sweeping a /24 or a 10,000-line target file finishes in seconds. Hosts are read as arguments, from a file (`-f`), or generated from a CIDR range (`-g`), and output can be filtered to alive (`-a`) or unreachable (`-u`) hosts, repeated on a loop (`-l`), or collected with per-host statistics (`-c`, `-C`, `-s`). It is the standard liveness primitive to pipe into port scanners and service probes. It needs raw-socket privilege — run as root or with `cap_net_raw` granted; unprivileged use is possible where the `ping_group_range` sysctl covers your group. It measures reachability and latency; it does not scan ports.

## Typical use cases

### Sweep a whole subnet for live hosts

Generate the target range and keep only the answers.

```
fping -a -g 198.51.100.0/24
```

Outcome: probes all 256 addresses of the range in parallel and prints the addresses that replied — a live-host list in a couple of seconds; pipe into a file to feed downstream tools.

### Ping a target list from a file

Use an existing asset list as input.

```
fping -a -f targets.txt
```

Outcome: probes every host named in `targets.txt` (one per line) and prints those that are alive, in list order rather than file order.

### Monitor hosts continuously

Watch a handful of systems over time instead of once.

```
fping -l 198.51.100.10 198.51.100.11
```

Outcome: sends probes to the given hosts forever, printing a reachability line whenever a host's state changes — a lightweight uptime watch; `-D` prefixes each line with a timestamp for logging.

### Measure loss and latency per host

Collect per-probe statistics for a small target set.

```
fping -c 5 -s 198.51.100.10 198.51.100.11
```

Outcome: sends 5 probes to each host, prints per-host loss/round-trip summaries, then an overall statistics block (`-s`); `-C 5` instead prints each individual round-trip time.

### List only the dead hosts

Invert the filter for inventory cleanup.

```
fping -u -q -g 198.51.100.0/24
```

Outcome: quietly probes the generated range and prints only addresses that did not answer — candidates for decommissioned or firewalled systems.

*No corpus evidence — derived from upstream documentation.*
