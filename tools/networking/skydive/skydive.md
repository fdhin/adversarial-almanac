---
name: "skydive"
categories:
  - blackarch-networking
version: "0.28.0"
homepage: "https://github.com/skydive-project/skydive"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-08-19"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "skydive-project/skydive"
github-stars: 2807
github-pushed: "2025-09-19"
github-archived: false
github-issues: 119
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# skydive

> Real-time network topology and protocol analyzer with flow capture and a Gremlin query interface.

## What it's for

Skydive models live network topology — interfaces, bridges, namespaces, containers — and captures flows on the wire, correlating both into one searchable graph. Agents run on each host, collect topology and flow data, and forward them to a central analyzer backed by Elasticsearch; a built-in Web UI renders the result and updates it live. It is SDN-agnostic but ships drivers that enrich the topology for common SDN solutions. Reach for it when you need to see how a host's networking is actually wired (bridges, veth pairs, overlays) and what traffic is crossing it right now; it is a monitoring and analysis platform, not an active scanner of remote networks.

## Typical use cases

### Run a single-node analyzer and agent

Start one process covering both roles on a lab host.

```
SKYDIVE_ETCD_DATA_DIR=/tmp SKYDIVE_ANALYZER_LISTEN=0.0.0.0:8082 sudo -E skydive allinone
```

Outcome: a combined analyzer plus agent starts and serves the Web UI on http://localhost:8082, where the local host's topology graph appears and can be explored live.

### Query the topology graph from the CLI

Ask the analyzer for the current graph without opening the UI.

```
skydive client query "g.V()"
```

Outcome: prints the JSON description of every vertex currently in the topology graph (hosts, interfaces, ports); Gremlin expressions filter it further, e.g. by node type or metadata.

*No corpus evidence — derived from upstream documentation.*
