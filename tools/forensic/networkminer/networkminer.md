---
name: "networkminer"
categories:
  - blackarch-forensic
version: "3.2"
homepage: "http://www.netresec.com/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-10"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "hatnetsec/NetworkMiner"
github-stars: 28
github-pushed: "2018-02-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# networkminer

> Netresec's passive network forensic analysis tool: feed it pcaps and get hosts, sessions, credentials and reassembled files.

## What it's for

NetworkMiner (by Netresec) is a GUI Network Forensic Analysis Tool (NFAT) that parses capture files and reconstructs what happened on the wire: endpoints with per-host details and OS fingerprinting, TCP/UDP sessions, credentials, DNS queries, parameters, messages, and files reassembled from transfers — all extracted passively from the pcap, with fingerprint databases shipped alongside. The packaged version is the free 3.2 edition, a .NET application that BlackArch runs under Mono through the `networkminer` wrapper. It is GUI-first: scripted batch analysis is the domain of the commercial NetworkMinerCLI (Professional edition), though the free edition does accept pcap paths and logging switches as command-line arguments to the executable itself.

## Typical use cases

### Launch the analyzer GUI

Open NetworkMiner and load captures interactively.

```
networkminer
```

Outcome: starts the NetworkMiner window under Mono (via the packaged wrapper); use File → Open to load a pcap and populate the Hosts, Sessions, Credentials, Files and other tabs.

### Load a pcap directly at startup

Skip the file dialog by passing the capture on the command line.

```
mono /usr/share/networkminer/NetworkMiner.exe capture.pcap
```

Outcome: NetworkMiner starts and immediately parses `capture.pcap` (one or more pcap paths can be given); the packaged `networkminer` wrapper does not forward arguments, so invoke the executable directly with `mono` to use this.

### Parse with debug logging enabled

Watch the parser work when a capture misbehaves.

```
mono /usr/share/networkminer/NetworkMiner.exe --debug capture.pcap
```

Outcome: parses the capture while logging debug output to standard out (`--debug`); use `--filelog` to log to a text file instead, and `--noupdatecheck` to skip the startup version check.

*No corpus evidence — derived from upstream documentation.*
