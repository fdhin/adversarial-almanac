---
name: "mrtparse"
categories:
  - blackarch-misc
version: "522.13cde30"
homepage: "https://github.com/t2mune/mrtparse"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "t2mune/mrtparse"
github-stars: 147
github-pushed: "2023-10-23"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mrtparse

> Python module and helper scripts for reading and analyzing MRT format BGP routing data (RFC 6396).

## What it's for

Parsing BGP routing dumps in the MRT format defined by RFC 6396 — the export format of FRRouting, Quagga, Zebra, BIRD, and OpenBGPD, and of the public Route Views and RIPE NCC archives. mrtparse decodes TABLE_DUMP, TABLE_DUMP_V2 (including ADD-PATH RIB subtypes) and BGP4MP/BGP4MP_ET records, with their peer tables, AS paths, communities, and all standard path attributes, into Python objects; gzip and bzip2 compressed files are read directly. It is primarily a library (`from mrtparse import *`), and the upstream examples/ directory ships ready-made converters to bgpdump text, JSON, YAML, and ExaBGP config/API format.

## Typical use cases

### Iterate over MRT entries in Python

Custom analysis of a routing dump in a script.

```python
from mrtparse import *

for entry in Reader("rib.20260101.0000.gz"):
    print(entry.data["type"], entry.data.get("subtype"))
```

Outcome: streams every MRT record of the (optionally gzipped/bzip2'd) dump through the loop, each parsed into the dict-like `entry.data` — here printing each entry's type and subtype, e.g. `16 BGP4MP`.

### Convert an MRT dump to bgpdump text format

Get a grep-friendly line-per-entry view of a dump.

```
mrt2bgpdump.py -M updates.20260101.gz
```

Outcome: prints one `BGP4MP|timestamp|A|peer|AS|prefix|as-path|...` line per entry with human-readable timestamps (`-m` for Unix timestamps); `-O file` writes to a file instead of stdout. The converter scripts ship in the upstream examples/ directory.

### Convert an MRT dump to JSON

Feed a routing dump into jq or a data pipeline.

```
mrt2json.py rib.20260101.0000.bz2
```

Outcome: emits a JSON array in which every entry carries its timestamp, type/subtype names, peer/local AS, addresses, and the fully decoded BGP message with path attributes.

### Replay a RIB dump into ExaBGP

Advertise dumped prefixes from a lab BGP speaker.

```
mrt2exabgp.py -A -4 198.51.100.10 rib.20260101.0000.gz
```

Outcome: outputs `announce route ...` lines in ExaBGP API format with the IPv4 next-hop rewritten to 198.51.100.10, which can be piped to an ExaBGP process; without `-A` it emits a full ExaBGP neighbor config instead.

*No corpus evidence — derived from upstream documentation.*
