---
name: "buttinsky"
categories:
  - blackarch-networking
version: "138.1a2a1b2"
homepage: "https://github.com/buttinsky/buttinsky"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "buttinsky/buttinsky"
github-stars: 82
github-pushed: "2013-06-04"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# buttinsky

> A modular Python framework for automated botnet monitoring, shipping an IRC monitor client and an interactive console.

## What it's for

An open-source framework (a 2012-era research project, Python 2) for automating botnet monitoring: instead of watching botnet control channels by hand, it connects monitoring clients to them, records what they observe, and is designed to distribute monitoring tasks across nodes with customisable protocols, behaviours and logging. In the current tree the implemented surface is an IRC protocol module over gevent, INI-style settings files that define a monitor's identity, and an interactive console that spawns monitors from those settings — further protocols were meant to be added as modules. Expect an early-stage framework rather than a finished product: development stopped in 2013 and the tree is skeletal.

## Typical use cases

### Spawn a botnet monitor from the interactive console

Start monitoring a control channel defined in the settings file.

```
cli
```

At the `>>` prompt run `monitor irc1`, where `[irc1]` is a section in `settings/template.set` (INI-style, with `nick =` and `host =` keys naming the monitor and its target server).

Outcome: spawns a gevent IRC client that connects to the configured server using that section's nick and host; observed data is written under the tool's `logs/` directory. `exit` leaves the console.

### Run the default monitor client directly

Skip the console.

```
buttinsky
```

Outcome: connects the IRC monitoring client defined by `settings/template.set` in one shot — the shipped file is an empty template, so fill in a nick and host first.

*No corpus evidence — derived from upstream documentation.*
