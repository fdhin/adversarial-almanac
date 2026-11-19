---
name: "wondershaper"
categories:
  - blackarch-networking
version: "48.98792b5"
homepage: "https://github.com/magnific0/wondershaper"
packaged-by: [blackarch]
blackarch-builddate: "2021-10-17"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "magnific0/wondershaper"
github-stars: 1901
github-pushed: "2024-07-25"
github-archived: false
github-issues: 44
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wondershaper

> One-command bandwidth limiter for a network interface, built on Linux tc — the continuation of Bert Hubert's original Wonder Shaper.

## What it's for

Wonder Shaper limits the download and upload bandwidth of one or more network adapters with a single command, wrapping iproute2's `tc` so you never write queueing rules by hand. Reach for it when bulk transfers saturate a link and wreck latency for interactive traffic — it uses hierarchical token bucket queueing (since v1.3) plus an improved ingress shaping method (v1.4) to keep response times low at the shaped rate. First released by Bert Hubert in 2002, it is maintained as magnific0's continuation, which is the version BlackArch packages; a CLI exists since v1.2. Rates are per interface only — it is not a per-IP or per-port shaper, and it needs root because it manipulates kernel queues.

## Typical use cases

### Cap download and upload on an interface

Stop bulk traffic from starving interactive traffic on a slow link.

```
wondershaper -a eth0 -d 8192 -u 4096
```

Outcome: Applies egress queueing and ingress rules limiting eth0 to 8192 Kbps download and 4096 Kbps upload; throughput tests clamp at those rates while SSH/browsing stay responsive. A `RTNETLINK answers: Operation not permitted` message means the command needs root.

### Show the limits currently in force

Inspect what shaping is applied to an interface.

```
wondershaper -s -a eth0
```

Outcome: Prints the tc setup wondershaper installed on eth0 — the queueing disciplines and classes that make up the current limits.

### Remove all shaping

Undo the limits and return the interface to unshaped operation.

```
wondershaper -c -a eth0
```

Outcome: Deletes every qdisc/filter/leaf wondershaper created on eth0; a following `wondershaper -s -a eth0` shows no shaping rules remain.

### Apply rates from the persistent config file

Use the settings that also survive reboots via the bundled systemd service.

```
wondershaper -p
```

Outcome: Reads `IFACE`, `DSPEED` and `USPEED` from the default preset file (/etc/systemd/wondershaper.conf, or another file passed with `-f`) and applies them to that interface — the same values the provided wondershaper.service re-applies at boot once enabled.

*No corpus evidence — derived from upstream documentation.*
