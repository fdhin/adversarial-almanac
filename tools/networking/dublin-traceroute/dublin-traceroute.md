---
name: "dublin-traceroute"
categories:
  - blackarch-networking
version: "0.4.2.r176.gd8a9e5b"
homepage: "https://github.com/insomniacslk/dublin-traceroute"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: cmake
license: BSD
meta-checked: "2026-11-09"
github-repo: "insomniacslk/dublin-traceroute"
github-stars: 301
github-pushed: "2026-10-08"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# dublin-traceroute

> NAT-aware multipath traceroute: maps every ECMP path in one run and flags hops that rewrite the flow.

## What it's for

Dublin Traceroute (a successor to Paris-traceroute) traces all paths through
load-balanced (ECMP) networks in a single run: it sends UDP probes with a
per-path flow variation so each load-balanced path is followed separately,
where a classic traceroute would merge or miss branches. Hops that alter the
flow vector — i.e. NAT gateways — are identified in the result. Output is a
JSON report (stdout, or a file via `--output-file`) that downstream tooling
such as the project's Python bindings can plot or analyze statistically. It
is path discovery only — not a port scanner or bandwidth tool — and needs
raw-socket privileges, so run it as root.

## Typical use cases

### Map all ECMP paths to a host

One run discovers every routing path and any NAT on the way.

```
sudo dublin-traceroute 198.51.100.10
```

Outcome: prints the hop-by-hop multipath tree for the target and ends with a
JSON report; hops that rewrite the probe flow are marked as NAT devices.

### Save the trace as JSON without DNS lookups

Produces a machine-readable trace for diffing or visualization.

```
sudo dublin-traceroute -N -o trace.json 198.51.100.10
```

Outcome: skips reverse-DNS resolution of hops and writes the full multipath
trace as JSON to `trace.json` instead of stdout.

### Trace through a NAT that breaks flow tracking

Recovers hops the default mode misses on no-payload-fixup NATs.

```
sudo dublin-traceroute -b 198.51.100.10
```

Outcome: re-derives the path tree assuming the NAT does not fix up payloads
(`--broken-nat`), typically revealing additional hops; `-i` instead varies
the source port rather than the destination port to generate paths.

*No corpus evidence — derived from upstream documentation.*
