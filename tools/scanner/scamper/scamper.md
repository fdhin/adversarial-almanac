---
name: "scamper"
categories:
  - blackarch-scanner
version: "20240920"
homepage: "http://www.caida.org/tools/measurement/scamper/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-11-17"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# scamper

> CAIDA's parallel Internet measurement utility: traceroute, ping, alias resolution and more, paced at a controlled packets-per-second rate.

## What it's for

scamper executes Internet measurement techniques — traceroute (including MDA), ping, alias resolution (`dealias`), HTTP(S), sting, tbit and others — against IPv4/IPv6 targets in parallel, filling a configured packets-per-second budget instead of firing everything at once. Results go to stdout as text by default, or to the binary `warts` format (optionally gz/bz2/xz-compressed) that records full metadata for archival and research; a family of `sc_*` utilities (`sc_warts2text`, `sc_warts2json`, `sc_warts2csv`, `sc_analysis_dump`, …) post-processes warts files. It is a topology/performance measurement workhorse, not a host or port scanner. The author's GitHub repo `cmand/scamper` hosts only the companion Python warts-parsing tools — the prober itself is distributed as tarballs from CAIDA's site.

## Typical use cases

### Traceroute a single target

One-off path measurement.

```
scamper -i 198.51.100.10 -c "trace"
```

Outcome: prints the traceroute result as text to the terminal; `-c` selects the measurement command (ping, trace, dealias, http, tracelb, …) and `-i` supplies addresses on the command line.

### Bulk-measure a target list at a fixed rate

Run many probes without flooding the network.

```
scamper -f targets.txt -c "ping" -p 100 -o pings.txt
```

Outcome: reads one address per line from `targets.txt`, probes them in parallel while holding the aggregate to 100 packets per second (`-p`), and writes text results to `pings.txt` (`-o`).

### Archive results in warts format

Keep full metadata for later analysis.

```
scamper -f targets.txt -c "trace" -O warts -o traces.warts
sc_warts2text traces.warts
```

Outcome: `-O warts` (or a `.warts` suffix on the output file) records the binary warts format; the companion `sc_warts2text` renders it back to human-readable form, and `sc_warts2json`/`sc_warts2csv` convert it for scripting.

*No corpus evidence — derived from upstream source (scamper-cvs-20240920 man pages `scamper(1)` and `sc_warts2text(1)`, distributed by CAIDA).*
