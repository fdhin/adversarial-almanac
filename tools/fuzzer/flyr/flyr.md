---
name: "flyr"
categories:
  - blackarch-fuzzer
version: "76.4926ecc"
homepage: "https://github.com/zznop/flyr"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "zznop/flyr"
github-stars: 49
github-pushed: "2018-12-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flyr

> fuzz lightyear — a block-based file-format fuzzing framework: describe a structure as typed blocks in a JSON model and let flyr write every mutation to disk as a numbered output file.

*No corpus evidence — derived from upstream documentation.*

## What it's for

flyr (by zznop, 2018) is a generation-based fuzzer for structured data. You describe a format — a pcap file, a protocol frame, a file header — as a JSON graph of typed blocks (numbers with explicit endianness, strings, raw bytes), and flyr applies its mutation strategies to those blocks, writing each mutated structure to the output directory with a sequence number and a configurable name suffix. It deliberately does not execute the target or monitor for crashes: the emitted corpus is replayed against your parser or consumer however your workflow likes. The shipped example model mutates pcap files.

## Typical use cases

### Generate a mutated corpus from a model

Run the bundled pcap model to produce a pile of malformed capture files.

```
flyr -f /usr/share/flyr/examples/pcap.json
```

Outcome: flyr validates the JSON model, applies the build actions block by block (magic number, version fields, IP/UDP headers, ...), processes the mutations, and writes the mutated files — named `<counter><name-suffix from the model>` (e.g. 00000032-lolwut.pcap) — into the model's output directory; `file *` on the results shows each file's corrupted header fields, and the files are ready to replay against any pcap parser under test.
