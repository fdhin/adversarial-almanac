---
name: "netmap"
categories:
  - blackarch-networking
version: "0.1.3"
homepage: "http://netmap.sourceforge.net/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-07-23"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# netmap

> Turns a batch of traceroutes into a GraphViz diagram of the network around you (NetMap by Wouter Godefroy).

## What it's for

NetMap 0.1.3 (Wouter Godefroy, belgoline.com; the SourceForge project of the same name) is a two-program pipeline for drawing the network surrounding a vantage point. `makelist` reads a list of target hosts and runs traceroute to each, storing the raw output under a data directory; `netmap` then digests those traceroute files recursively, derives the graph of IP interconnections, optionally resolves hops to hostnames (with caching), and emits a GraphViz `.dot` file you open with dotty. It needs only traceroute, awk/grep and GraphViz. Identity note: this package is NOT luigirizzo's netmap — the kernel packet-I/O framework used by high-speed capture and traffic-generation tools. The two projects share only the name; this one builds and draws network maps.

## Typical use cases

### Collect traceroute data for your targets

```
makelist
```

Outcome: runs traceroute for every host listed in hosts.txt in the working directory and writes each result under ./netdata/ (both configurable), producing the raw input netmap consumes. Edit hosts.txt to your target list first.

### Generate and view the network diagram

```
netmap -d netdata -o netmap.dot
dotty netmap.dot
```

Outcome: reads every traceroute file under netdata/ recursively, resolves hops to hostnames with a local cache, and writes the interconnection graph to netmap.dot; dotty then renders the editable map, grouping runs of unresponsive hops into single labeled nodes.

### Build the map fast, without name resolution

```
netmap -n -d netdata
```

Outcome: produces the same graph with bare IP labels but skips every DNS lookup, which is markedly faster on large target sets; `-m 3` additionally prunes the graph to nodes at most three hops from the start.

*No corpus evidence — derived from upstream documentation.*
