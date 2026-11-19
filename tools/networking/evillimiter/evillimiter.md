---
name: "evillimiter"
categories:
  - blackarch-networking
version: "36.46d2033"
homepage: "https://github.com/bitbrute/evillimiter"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "bitbrute/evillimiter"
github-stars: 2026
github-pushed: "2026-03-24"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# evillimiter

> ARP-spoofing bandwidth limiter: throttle, block or free any device on the LAN from one console.

## What it's for

evillimiter monitors, analyzes and limits the bandwidth of devices on a
local network without administrative access to them: it places itself
between hosts and the default gateway via ARP spoofing, then shapes the
relayed traffic with iptables/tc. From its interactive console you scan
the subnet, address hosts by ID and limit, block or release their
connectivity, and watch per-host usage. It works on IPv4 only (ARP
spoofing is IPv4-specific) and on Linux; the operator's machine must stay
in the traffic path for limits to keep applying. It does not touch the
router itself.

## Typical use cases

### Start the console and discover hosts

Enter the tool and enumerate live devices before acting.

```
sudo evillimiter
```

Outcome: opens the interactive console with interface, gateway and netmask
auto-detected (override with `-i`, `-g`, `-m`, `-n`; `-f` flushes leftover
iptables/tc state first); run `scan` inside to enumerate live hosts and
`hosts` to list them with the IDs every other command uses.

### Throttle a host's bandwidth

Cap a device's up/down speed by its host ID.

```
limit 4,5 200kbit
```

Outcome: ARP-spoofs hosts 4 and 5 and enforces a 200 kbit cap on their
traffic through your machine; rates accept bit/kbit/mbit/gbit units, IDs
can be a list or `all`, and `--upload`/`--download` restrict the limit to
one direction.

### Cut and restore access

Block a device entirely, then undo every restriction.

```
block 4
```

Outcome: drops host 4's connectivity through the gateway (optionally per
direction with `--upload`/`--download`); `free all` later removes all
limits and blocks and stops spoofing.

### Measure usage before limiting

Find who actually consumes the bandwidth.

```
analyze 2,3 --duration 120
```

Outcome: passively meters hosts 2 and 3 for 120 seconds without limiting
them; once limited, `monitor` shows live and cumulative usage of each
throttled host.

*No corpus evidence — derived from upstream documentation.*
