---
name: "netzob"
categories:
  - blackarch-reversing
version: "1.0.2"
homepage: "https://github.com/netzob/netzob/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "netzob/netzob"
github-stars: 840
github-pushed: "2024-04-18"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netzob

> Reverse-engineer, model, generate and fuzz communication protocols from captured traffic.

## What it's for

Netzob is a protocol reverse-engineering framework initiated by security auditors at AMOSSYS together with the CIDre research team of CentraleSupélec. It infers a protocol's message format (the vocabulary) and state machine (the grammar) from observed communication — passively, by importing existing traces (PCAP captures, structured files, IPC logs), or actively, by interacting with a live implementation — using bio-informatics-inspired algorithms (Needleman-Wunsch alignment, UPGMA clustering) to split messages into fields and group equivalent messages. The inferred model then drives traffic generation (Netzob plays client, server or both) and format-aware fuzzing, with mutation and generation strategies over both message layout and state machine. Models export toward Wireshark and Scapy dissectors and Peach/Sulley fuzzers. It is not a network scanner or a generic fuzzer: everything starts from samples of the target protocol. Current releases are a Python 3 library (3.8+) with an interactive CLI — the old Eclipse-style GUI is gone.

## Typical use cases

### Use Netzob as a Python library

Every workflow — import, inference, generation, fuzzing — is Python API.

```python
from netzob.all import *
```

Outcome: pulls the complete API (symbols, fields, variables, importers, actors) into a script or an IPython session; this single import is the documented entry point for building an inference or fuzzing pipeline in code.

### Drive the API interactively

Experiment with captured data without writing a script.

```
netzob
```

Outcome: starts Netzob's own interactive shell, where the same API can be exercised statement by statement — handy for stepping through message import and field inference before committing to a script.

*No corpus evidence — derived from upstream documentation.*
