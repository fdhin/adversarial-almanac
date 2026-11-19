---
name: "topera"
categories:
  - blackarch-scanner
version: "19.3e230fd"
homepage: "https://github.com/toperaproject/topera"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "toperaproject/topera"
github-stars: 35
github-pushed: "2014-01-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# topera

> IPv6 security toolkit whose scanning and slow-HTTP attacks were built to evade Snort detection.

## What it's for

Topera is an IPv6 security analysis tool by Daniel Garcia (cr0hn) and Rafa Sanchez: a plugin-based Python 2/scapy framework whose modules — a slow-HTTP "loris" attack over IPv6 and a TCP port scanner — deliberately craft traffic that Snort, the IDS/IPS engine also embedded in commercial products, could not detect at the time, to demonstrate the risk of unmonitored IPv6. The project is unmaintained (final release 0.0.2, 2013) and tied to Python 2 and an old scapy, so treat it as a historical/awareness tool: modern IDS rulesets are likely to catch its patterns. It still runs through the `topera` wrapper (python2 topera.py).

## Typical use cases

### List available plugins

```bash
topera -L
```

Outcome: prints the attack and scan plugins compiled into the installed version, giving the module names you can pass to `-M`.

### Run a Slowloris-style attack over IPv6

```bash
topera -M topera_loris -t 2001:db8::10 --dport 8080 --delay 0 --headers-num 0 -vvv
```

Outcome: opens slow, header-dribbled HTTP connections to the IPv6 target with the chosen destination port, `--delay` seconds between connections and `--headers-num` IPv6 extension headers per packet; `-vvv` prints verbose progress.

### Port-scan over IPv6

```bash
topera -M topera_tcp_scan -t 2001:db8::10 -p 21,22,80,443,8080 --scan-delay 0 --headers-num 0 -vvv
```

Outcome: TCP port scan of the listed ports against the IPv6 target, pacing probes with `--scan-delay` and optionally carrying them inside extension headers.

*No corpus evidence — derived from upstream documentation (toperaproject/topera README and GitHub Pages project page; BlackArch PKGBUILD wraps `python2 topera.py` as `topera`).*
