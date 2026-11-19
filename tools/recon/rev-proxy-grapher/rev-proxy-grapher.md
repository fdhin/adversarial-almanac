---
name: "rev-proxy-grapher"
categories:
  - recon
version: "0~git20180301-0parrot1"
homepage: "https://github.com/mricon/rev-proxy-grapher"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "mricon/rev-proxy-grapher"
github-stars: 399
github-pushed: "2024-07-28"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rev-proxy-grapher

> Renders a Graphviz diagram of your reverse-proxy topology from a hand-written YAML description, optionally enriched with nmap XML data.

## What it's for

rev-proxy-grapher turns a manually curated YAML file describing external addresses, proxies and internal networks into a graphviz diagram of the traffic flow — useful for documenting "what actually fronts what" in a DMZ. It can merge in nmap XML scan output to annotate backend nodes with observed ports and services, resolve DNS names onto nodes, and limit the graph to specific source networks to cut noise. Output format is inferred from the `--out` file extension (png, svg, pdf — anything graphviz supports). It models exactly one level of proxying (no haproxy→nginx→backend chains), and the topology YAML is the source of truth: the tool draws what you declare, it does not discover it.

## Typical use cases

### Draw the basic topology diagram

```
rev-proxy-grapher.py --topology topology.yaml
```

Outcome: parses the YAML topology (external nets, proxies, internal nets) and writes `graph.png` — the default output — showing the external-to-internal proxy flow; run it against the bundled `examples/topology.yaml` first to see the expected YAML shape.

### Enrich backend nodes with nmap results

```
rev-proxy-grapher.py --topology topology.yaml --nmap-xml nmap-external.xml --out graph-with-nmap.svg
```

Outcome: nodes picked up from the nmap XML scan carry their detected open ports and services in the diagram, and the graph is written as `graph-with-nmap.svg` (format follows the extension).

### Focus the graph on specific sources

```
rev-proxy-grapher.py --topology topology.yaml --resolve-dns --limit-ext 198.51.100.0/24
```

Outcome: only traffic originating from the listed source IPs/networks is included and every node gets its DNS name resolved where possible — decluttering large topologies down to the customer or partner paths you care about.

*No corpus evidence — derived from upstream documentation.*
