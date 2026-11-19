---
name: "nili"
categories:
  - blackarch-scanner
version: "0.1.1.r4.g285220a"
homepage: "https://github.com/niloofarkheirkhah/nili"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "niloofarkheirkhah/nili"
github-stars: 219
github-pushed: "2019-10-23"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nili

> Python toolkit that chains LAN discovery, port scanning, man-in-the-middle capture, protocol reverse engineering and fuzzing into one command set.

## What it's for

Nili bundles the sequence a protocol tester typically walks: find live devices on a segment, port-scan a target, capture its traffic as a man in the middle, reverse-engineer the message format of the captured protocol, then fuzz that format. It is built on Scapy (packet-level work) and Netzob (protocol inference and fuzzing), and each of its five modes is selected by a flag on the single `nili` command. It is not a web or CVE scanner — it works at packet/protocol level and reports nothing about known vulnerabilities. The upstream is a 2017–2019 research-era project (demo version banner in source); expect Python-3-era dependencies. Network scan, port scan and MITM modes need root.

## Typical use cases

### Discover devices on a LAN

Sweep a subnet for live hosts from a given interface.

```
sudo nili -N -r 198.51.100.0/24 -i eth0
```

Outcome: prints the responsive devices found on the segment; each mode has extended help via `-NH` (also `-PH`, `-MH`, `-RH`, `-FH` for the other modes).

### Port scan a host

Check which ports a single device answers on.

```
nili -P -t 198.51.100.10 -mp 1 -xp 1024
```

Outcome: reports open TCP ports on the target between the minimum and maximum port bounds.

### Capture a target's traffic as man in the middle

Interpose on a victim's traffic and filter what to capture.

```
sudo nili -M -t 198.51.100.10 -g 198.51.100.1 -i eth0 -f "tcp port 80"
```

Outcome: runs the ARP-based MITM between target and gateway, capturing traffic matching the BPF filter; the capture feeds the protocol-reversing step.

### Reverse-engineer a protocol from captured messages

Infer message structure from observed traffic.

```
nili -R -d ";"
```

Outcome: Netzob-based analysis of the captured data, split into fields by the given delimiter, producing an inferred message format you can then fuzz.

### Fuzz a field of a protocol

Mutate one field's length and content against a service.

```
nili -F -d ";" -c "COMMAND" -l 1 -ml 1 -xl 64 -t 198.51.100.10 -p 8080
```

Outcome: sends generated mutations of the selected field (lengths between `-ml` and `-xl`) delimited by `-d` to the target service and reports how it responds — crashes or errors are the interesting outcomes.

*No corpus evidence — derived from upstream source (argparse interface in `nili/__main__.py` of niloofarkheirkhah/nili; console entry point `nili` from setup.py).*
