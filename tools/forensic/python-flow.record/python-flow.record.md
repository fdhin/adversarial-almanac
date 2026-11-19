---
name: "python-flow.record"
categories:
  - blackarch-forensic
version: "3.21.r16.ge6b58fd"
homepage: "https://github.com/fox-it/flow.record"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "fox-it/flow.record"
github-stars: 11
github-pushed: "2026-11-06"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# python-flow.record

> Fox-IT's Dissect-framework record library: typed, streamable forensic records, with the rdump CLI to read, filter and convert them.

## What it's for

flow.record is the Python library (from Fox-IT's Dissect team) that defines the record format used throughout the Dissect tooling: structured, typed data items (IP addresses, strings, timestamps, ...) described by a RecordDescriptor schema, which can be streamed to disk as compressed `.records.gz` files or piped between tools. Its primary forensic role is as the output side of target-query and other Dissect tools — their findings arrive as records on stdout. The bundled rdump utility reads record files or stdin, prints them, filters them with Python-syntax selectors, and converts them through output adapters such as CSV and JSON. It is a data-handling layer, not an acquisition tool: it neither images disks nor parses artifacts itself.

## Typical use cases

### Capture target-query output as records

Pipe Dissect artifact results through rdump for a quick look.

```
target-query -f runkeys targets/EXAMPLE.vmx | rdump
```

Outcome: each RunKey entry surfaces as one human-readable record line (hostname, key path, value, timestamp, user SID), letting you eyeball autostart persistence before saving anything.

### Filter records with a selector

Select only the records of interest from a saved record file.

```
rdump output.records.gz -s '"google" in r.description'
```

Outcome: prints only the records whose `description` field contains "google"; selectors use Python-like syntax (string membership, `r.ip in net.ipnetwork(...)`, field comparisons) and run entirely offline against the record file.

### Write custom records from Python

Define a schema and stream your own typed records to disk.

```
python3 - <<'PY'
from flow.record import RecordDescriptor, RecordWriter

MyRecord = RecordDescriptor("my/record", [
    ("net.ipaddress", "ip"),
    ("string", "description"),
])

with RecordWriter("output.records.gz") as writer:
    writer.write(MyRecord("198.51.100.10", "example host"))
PY
```

Outcome: creates output.records.gz containing one typed record; read it back with `rdump output.records.gz` or a RecordReader in your own code — the schema travels with the data.

*No corpus evidence — derived from upstream documentation.*
