---
name: "infip"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/104927/infIP.1-Blacklist-Checker.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# infip

> Debasish Mandal's 2011 blacklist checker: takes the remote hosts your machine is actively talking to (netstat output) and looks each one up against Spamhaus RBLs, producing an HTML report.

*No corpus evidence — derived from upstream documentation (author's release notes for infIP 0.1; behavior per the packaged Python 2 script).*

## What it's for

infIP answers a malware-triage question: of all the hosts my computer is connected to right now, are any known-bad? It harvests active connections via `netstat`, resolves the remote peers, and queries each address against Spamhaus real-time blocklists (SBL/XBL/PBL), then writes an HTML report listing every host with its listing details and opens it in a browser — on an infected machine, C2 or spam-drop infrastructure often shows up listed. It is a Windows-era Python 2.7 script (author instructions target `C:\Python27`) with no command-line flags: running it performs the whole pipeline. Treat it as a quick situational indicator, not a verdict — RBL absence proves nothing, and a modern alternative is checking the same Spamhaus zones per-IP or with a maintained DNSBL tool.

## Typical use cases

### Check current connections against Spamhaus

```
infip
```

Outcome: Runs the full pipeline unattended — collects `netstat` output, resolves each remote host, queries the Spamhaus blocklists, and writes and opens an HTML report where every host links to its listing details; hosts appearing listed are the ones to investigate as possible malware callbacks.
