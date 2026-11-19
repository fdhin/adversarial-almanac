---
name: "sstat-git"
categories:
  - misc
version: "20141023.r8"
homepage: "https://bitbucket.org/R4v3N/sstat.sh"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sstat-git

> Prompt-driven shell watchdog that polls a web server from outside while you fuzz or load-test it, and tells you the moment it stops responding.

## What it's for

sstat (R4v3N / Top-Hat-Sec; scripts `sstat.sh` and `sstat-parser.sh`) watches a web server's external availability while it is being stress-tested, fuzzed, or otherwise loaded. It prompts for a hostname, a polling interval in seconds, and whether to enable logging; it then resolves the host's IP and checks it through the public isup.me service in a loop, printing SERVER ONLINE or SERVER OFFLINE each round and playing an audio alert when the site reads as down. With logging on, every check is appended to `~/sstat-logs/<session-timestamp>.<host>.log`; the companion `sstat-parser.sh` summarizes those logs. It has no command-line options — it is entirely interactive — and it only monitors: the load itself comes from whatever other tool you run.

## Typical use cases

### Watch a target during a load test

Keep an independent eye on a host while another tool hammers it.

```
sstat.sh
```

Outcome: prompts for the domain/web address, the seconds between checks, and logging (y/n); then prints the server name, resolved IP and ONLINE/OFFLINE status each interval. With logging enabled, status lines accumulate in `~/sstat-logs/` and an audible alert plays when the site goes down.

### Summarize a long sstat log

Distill hours of polling output into the offline windows and IPs seen.

```
sstat-parser.sh
```

Outcome: a small menu (parse logs, or remove all logs and temp files); parsing a log prints the server name, any OFFLINE occurrences with surrounding context, the timestamps it reported online, and every IP observed.

*No corpus evidence — derived from upstream documentation.*
