---
name: "python2-darts.util.lru"
categories:
  - blackarch-misc
version: "7.5ef01b1"
homepage: "https://pypi.python.org/pypi/darts.util.lru"
packaged-by: [blackarch]
blackarch-builddate: "2020-02-11"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# python2-darts.util.lru

> Fixed-capacity dictionary that automatically evicts least-recently-used entries.

## What it's for

A small Python library, not a standalone program: it provides `LRUDict`, a dictionary with a maximum capacity that evicts the least-recently-used entry whenever inserting a new one would exceed it. The capacity is set at construction time (minimum 1) and can be changed at runtime through the `capacity` attribute; shrinking the capacity immediately discards the least recently used entries to fit. In security distributions this package exists purely as a Python 2 dependency for other tools that need a bounded in-memory cache. There is no command-line interface, and note that the import path (`darts.lib.utils.lru`) differs from the package name.

## Typical use cases

### Keep a bounded LRU cache in a Python 2 script

Demonstrates the eviction behavior that dependent tools rely on.

```
python2 -c "
from darts.lib.utils.lru import LRUDict
cache = LRUDict(2)
cache[1] = 'first'
cache[2] = 'second'
cache[3] = 'third'
print(sorted(cache.iterkeys()))
"
```

Outcome: prints `[2, 3]` — inserting the third entry evicted key 1, the least recently used; `len(cache)` never exceeds the capacity, and setting `capacity` below 1 raises `ValueError`.

*No corpus evidence — derived from upstream documentation.*
