---
name: "networkmap"
categories:
  - blackarch-networking
version: "58.f5faf17"
homepage: "https://github.com/lorenzog/NetworkMap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lorenzog/NetworkMap"
github-stars: 26
github-pushed: "2016-07-06"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# networkmap

> Post-exploitation network mapper that turns command output dumped on compromised hosts into a growing network diagram.

## What it's for

NetworkMap builds a picture of a network by collating network information dumped on remote hosts — routing tables, ARP tables, traceroute output — instead of by scanning. The workflow is post-exploitation: land on a host, save the output of ordinary system commands to a file, feed the dump to this tool, then move to the next host and repeat. Every run merges its findings into a persistent `networkmap.json` knowledge base and renders the accumulated hosts and links as a Graphviz diagram. It parses evidence only — it never sends packets or probes the network itself, and it does not enumerate services.

## Typical use cases

### Build the network graph from an ARP table dump

Feed a text dump of a host's ARP table into the tool.

```
python2 networkmap arp_table.txt
```

Outcome: the dump is parsed, discovered neighbors are merged as nodes and edges into `networkmap.json`, and a Graphviz rendering of the current network picture is generated; each later run adds to the same model.

### Ingest a traceroute dump from a known vantage host

Traceroute output is only meaningful when the tool knows which host produced it.

```
python2 networkmap traceroute.txt --ip 198.51.100.10
```

Outcome: the hop sequence is attributed to vantage point `198.51.100.10` and merged into the model, adding the discovered intermediate hops as network nodes.

### Serve the generated diagram for browser review

Process another dump and automatically start a local web server afterwards.

```
python2 networkmap arp_table.txt --ip 198.51.100.10 -H
```

Outcome: after the run completes, Python's SimpleHTTPServer starts; browsing to `http://localhost:8000` shows the generated diagram files for interactive review.

*No corpus evidence — derived from upstream documentation.*
