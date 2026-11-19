---
name: "chopshop"
categories:
  - blackarch-networking
version: "444.8bdd393"
homepage: "https://github.com/MITRECND/chopshop"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "MITRECND/chopshop"
github-stars: 493
github-pushed: "2022-12-19"
github-archived: true
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chopshop

> MITRE's protocol-analysis framework: run pynids-based decoder/detector modules over pcaps or live traffic.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ChopShop is a MITRE-developed framework (ChopShop 4, Python 2 on libnids/pynids) for creating and running modules that decode network traffic and detect adversary tradecraft. ChopShop itself does no analysis — it reassembles sessions from pcaps or a live interface and streams them through analyst-written modules, which can be combined with a small expression syntax (semicolons run modules side by side, pipes chain them, parens tee them). Reach for it when you need repeatable pcap triage: carving payloads, decoding C2 protocols, or hunting for known malware traffic signatures. Module availability is everything — with no matching module installed it processes nothing. Development is dormant (perpetual beta, Python 2 era).

## Typical use cases

### Run one module over a pcap with a BPF filter

Analyze only the sessions you care about.

```
chopshop -f capture.pcap "host 198.51.100.10" "payloads"
```

Outcome: every session in capture.pcap matching the BPF filter is passed through the `payloads` module, which prints the stream payloads to stdout.

### Run several modules side by side

Try multiple decoders over the same traffic in one pass.

```
chopshop -f capture.pcap "payloads; gh0st_decode"
```

Outcome: both modules process all sessions independently and keep separate output — useful when the protocol in the capture is uncertain.

### Save module output to per-module files

Capture each module's findings for the report.

```
chopshop -F "output/%N.txt" -f capture.pcap "payloads; gh0st_decode"
```

Outcome: writes `output/payloads.txt` and `output/gh0st_decode.txt` (`%N` expands to the module name, `%T` to a timestamp). stdout is suppressed unless `-S` is also given.

### Chain modules with a pipe

Feed one module's output into the next stage.

```
chopshop -i eth0 "http | http_extractor"
```

Outcome: live traffic on eth0 is parsed by `http`, and the extracted HTTP objects are handed to `http_extractor`, which prints the data it finds.

### Tee several streams into one detector

Feed multiple protocol streams into a single detection module.

```
chopshop -f malware.pcap "(dns, icmp) | malware_detector"
```

Outcome: DNS and ICMP traffic are both routed into `malware_detector`; a warning appears if a child module cannot accept the parent's data type.

### Process a continuous list of pcaps

Keep analysis running as new capture files arrive.

```
chopshop -f filelist.txt -l -L "host 198.51.100.10" "payloads"
```

Outcome: `-l` treats filelist.txt as a list of pcap paths and `-L` keeps reading it forever (it can be a FIFO fed by another process) until interrupted with Ctrl-C.
