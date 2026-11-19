---
name: "batman-alfred"
categories:
  - blackarch-wireless
version: "2025.2"
homepage: "http://www.open-mesh.org/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "open-mesh-mirror/alfred"
github-match: "exact"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 15
github-pushed: "2026-11-05"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
github-commits-year: 64
gh-activity-checked: "2026-11-09"
---

# batman-alfred

> A.L.F.R.E.D. — the userspace daemon that distributes arbitrary local facts (hostnames, GPS, topology data) across a batman-adv mesh with zero configuration.

## What it's for

alfred ("Almighty Lightweight Fact Remote Exchange Daemon") is the data-distribution companion to the [[batman-adv]] kernel module, originally written to replace batman-adv's visualization system. Each node runs an alfred server; local processes publish typed data blobs to it over a unix socket, and alfred replicates everything between nodes using IPv6 link-local multicast (or optionally IPv4 multicast via `-4`), needing no addresses or configuration. Data is transient — it must be re-published or alfred forgets it after 10 minutes. The package also builds `batadv-vis`, which publishes batman-adv neighbor/client tables through alfred and renders them as a Graphviz topology, and `alfred-gpsd` for feeding GPS data in. This is mesh-network plumbing, not an assessment tool.

## Typical use cases

### Start the daemon in primary mode

Run a data-exchange server on the mesh.

```
sudo alfred -i bat0 -m
```

Outcome: the daemon runs in primary mode — it announces itself so secondary-mode nodes on other mesh nodes forward their data to it, and primaries sync with each other; `-b` selects a non-default batman-adv interface (`bat0` is the default, `-b none` disables batman-adv-based server selection).

### Publish a local fact

Announce this node's hostname as data type 64.

```
cat /etc/hostname | alfred -s 64
```

Outcome: stores the piped bytes under data type 64 (types 0–63 are reserved; `-V` optionally tags a version others can filter on); re-run periodically or the entry expires after 10 minutes.

### Retrieve facts mesh-wide

Collect what every node has published under a type.

```
alfred -r 64
```

Outcome: prints one line per contributing node in the form `{ "fe:f1:00:00:01:01", "mesh-node-1\x0a" },` — source MAC plus raw payload — giving every alfred host's view of the whole mesh's data of that type.

### Publish the mesh topology

Feed batman-adv neighbor and client tables into alfred.

```
sudo batadv-vis -i bat0 -s
```

Outcome: the vis server reads batman-adv's neighbor and local-client tables every 10 seconds and publishes them through the running alfred daemon, so topology accumulates mesh-wide.

### Render the topology as a graph

Dump the collected topology in Graphviz form.

```
batadv-vis
```

Outcome: prints a `digraph { ... }` describing every mesh node, its links (labelled with the routing metric) and attached clients (`[label="TT"]`), ready to pipe into `dot` for a picture of the mesh.

*No corpus evidence — derived from upstream documentation.*
