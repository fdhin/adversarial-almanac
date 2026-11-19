---
name: "pcapfex"
categories:
  - blackarch-networking
version: "60.c51055a"
homepage: "https://github.com/vikwin/pcapfex"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "vikwin/pcapfex"
github-stars: 231
github-pushed: "2020-02-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pcapfex

> Carves transferred files out of packet captures with protocol-agnostic heuristics.

## What it's for

pcapfex (Packet CAPture Forensic Evidence eXtractor) finds and extracts files transferred inside pcap captures, even when non-standard or undocumented protocols carried them. It walks the capture's streams, recognizes known file formats and encodings, and writes a structured export of everything it recovers; an entropy-based mode additionally carves raw data blobs that match no known signature. Its plugin system lets Python developers add new file types, encodings, or protocol dissectors. Note that it is a Python 2.7 codebase (the package ships a python2 wrapper script) and it only reads capture files — it does not sniff live interfaces.

## Typical use cases

### Extract all files from a capture

Recover documents, images, and archives transferred in a session.

```
pcapfex capture.pcap
```

Outcome: scans the capture and writes every recognized file into the default `output/` folder, organized by the flow it came from; pass a second positional argument to redirect the extraction folder.

### Handle captures taken on the sending host

Extract successfully despite offloaded TCP checksums.

```
pcapfex -nv capture.pcap
```

Outcome: disables IP/TCP/UDP checksum verification, so captures recorded on a machine that was itself sending traffic (whose NIC offloaded checksum computation, storing invalid values in the file) still yield their files.

### Carve unrecognized data by entropy

Recover blobs that match no known file signature.

```
pcapfex -e capture.pcap carved/
```

Outcome: runs entropy-based raw-data extraction in addition to format-aware carving, writing candidate data blobs into `carved/` for manual inspection.

*No corpus evidence — derived from upstream documentation.*
