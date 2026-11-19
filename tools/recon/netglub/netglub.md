---
name: "netglub"
categories:
  - recon
version: "1.0"
homepage: "http://www.netglub.org"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "suduma/netglub"
github-stars: 3
github-pushed: "2018-04-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netglub

> An open-source Maltego-style intelligence-gathering framework: a master/slave network of "transforms" driven by a Qt desktop client.

## What it's for

Netglub ("the really open source information gathering solution", netglub.org, 1.0 released February 2011; developed by DIATEAM) is a link-analysis OSINT platform built on the same idea as Maltego: entities (domains, people, IPs) are fed through *transforms* — small plugins that query data sources and return related entities — and the results are graphed in a client. Its architecture is a master daemon that owns transforms, entities, sessions and TLS credentials and exposes an XML-RPC interface; slave daemons that register with a master and actually execute the transform plugins; and a Qt GUI client (the `qng` component) for interactive investigations. The project is dormant: the last release dates from 2011 and users reported build breakage against later Qt/graphviz versions, so treat it as a historical alternative to Maltego, not a maintained one. Modern equivalents (Maltego CE, SpiderFoot) fill the same role with current sources.

## Typical use cases

### Start the master daemon

Bring up the XML-RPC hub that serves transforms and entities to clients and slaves.

```
netglub-master -a /usr/share/netglub/master/transforms/ -f /usr/share/netglub/master/entities/ -s /etc/netglub/master_creds/tls_credentials/
```

Outcome: the master daemon starts, loads the transform definitions (`-a`), entity types (`-f`), and TLS certificate/key directory (`-s`), and begins listening for the GUI and for slaves over XML-RPC (long option forms: `--transforms-path`, `--entities-path`, `--tls-creds-dir`).

### Attach a slave to the master

Add a worker that runs the actual transform plugins.

```
netglub-slave -t /usr/share/netglub/slave/transforms/ -u /usr/share/netglub/slave/plugins/ -m 198.51.100.10
```

Outcome: the slave registers with the master at `-m` (the master host running the daemon above), advertising the transforms found in `-t` and loading plugin executables from `-u`; transforms invoked in the GUI are dispatched to this slave and their results flow back through the master. `-c` sets the reconnection timeout if the link drops.

*No corpus evidence — derived from upstream documentation (flag sets read from the packaged master/slave `main.cpp` getopt tables; architecture and history from netglub.org).*
