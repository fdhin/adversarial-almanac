---
name: "tor-autocircuit"
categories:
  - blackarch-defensive
version: "0.2"
homepage: "http://www.thesprawl.org/projects/tor-autocircuit/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "iphelix/tor-autocircuit"
github-stars: 12
github-pushed: "2018-11-23"
github-archived: true
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tor-autocircuit

> Builds Tor circuits to a precise specification — per-hop countries, path length, crossings, bandwidth — instead of accepting Tor's default rotation.

## What it's for

tor-autocircuit gives finer control over Tor circuit creation than the Tor client exposes natively. Using the TorCtl library it connects to Tor's local control port and builds a pool of circuits matching constraints defined directly in the script's Configuration section: number of circuits, unique countries per route, maximum continent/ocean crossings, entry/middle/exit country codes, excluded countries, path length, bandwidth percentiles, and a named exit node. It disables Tor's predicted circuits and attaches streams to the circuits it builds, so traffic actually follows the constructed paths. Configuration is done by editing the script — there are no command-line flags. It is a Python 2 / TorCtl-era research tool and expects a local Tor with its control port (9051) enabled.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Build a pool of custom circuits

Have Tor use circuits you specified rather than its own rotation.

```
tor-autocircuit
```

Outcome: the script connects to the local Tor control port (9051), subscribes to circuit/stream events, and builds the configured pool — by default ten three-hop circuits that do not reuse a country within a route — then keeps running and attaches new streams to those circuits; interrupt with Ctrl-C to detach and exit. If the control port is not reachable it prints `[!] Couldn't connect to TOR.` and exits, which means enabling `ControlPort 9051` in torrc (or starting Tor with `--torcontrol 9051`) first.

### Constrain circuit geography and length

Pin where a circuit enters, transits and exits.

```
exit_country       = 'DE'
entry_country      = 'NL'
pathlen            = 3
num_circs          = 20
```

These lines are edited in the Configuration section of the script itself (a `GeoIPConfig` for countries and a `SelectionManager` for path/bandwidth parameters).

Outcome: on the next run only circuits matching the constraints are built — here up to twenty three-hop circuits entering via the Netherlands and exiting via Germany — with `excludes` for banned countries, `unique_countries`, crossing limits, minimum bandwidth (`min_bw`) and bandwidth percentiles (`percent_fast`/`percent_skip`) available for further narrowing.
