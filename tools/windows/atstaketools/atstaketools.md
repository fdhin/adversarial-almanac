---
name: "atstaketools"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://packetstormsecurity.com/files/50718/AtStakeTools.zip.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/AtStakeTools"
github-stars: 0
github-pushed: "2019-03-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# atstaketools

> Archive of the classic @stake Windows security tools — fuzzers, enumeration and password-audit utilities, recovery kits and forensics from the consultancy's late-'90s/2000s heyday.

## What it's for

Not a single program but a curated archive (distributed via Packet Storm; the Seabreg/AtStakeTools GitHub repo mirrors the identical tree) of tools released by @stake, the security consultancy that absorbed the L0pht crew. It is organized by task: Vulnerability Scanning & Analysis (spike, COMbust, feszer, sharefuzz, NetScan, the WAP assessment toolkit, wfpdisable, AUSTIN), Information Gathering (dcetest, nbtdump, ncpquery, redfang, rpcdump), Network Utilities (netcat as nc.exe plus the nc110/nc11nt packages), Password Auditing (ipivot, notsync), Recovery & Restoration (beamcrack, DeCRYPTO, eidextract, heimlich, iSpy) and Forensics (PDAZap, pdd). Members are a mix of Windows executables and source tarballs from an era when Windows NT was the default assessment target; several became classics with their own lineages (spike the fuzzer, redfang the Bluetooth discoverer). BlackArch unpacks the whole tree under /usr/share/windows/atstaketools/.

## Typical use cases

### Survey the archive by category

```
ls /usr/share/windows/atstaketools/
```

Outcome: prints the archive's table of contents — the six category folders (Vulnerability Scanning&Analysis Tools, Information Gathering, Network Utilities, Password Auditing tools, Recovery&Restoration Tools, Forensics) that map each bundled tool to its job.

### Locate a bundled tool

```
find /usr/share/windows/atstaketools/ -iname '*nc*'
```

Outcome: tracks down the bundled netcat variants under Network Utilities (nc.exe, nc11nt.zip, nc110.tgz); the same name search finds any of the archive's members — rpcdump, redfang, spike, pdd and the rest.

### Inspect a member without unpacking it

```
unzip -l "/usr/share/windows/atstaketools/Network Utilities/nc11nt.zip"
```

Outcome: lists the inner per-tool archive's contents — the Windows netcat binaries and their docs — without extracting anything; most members are self-contained zip/tar packages of one tool, so this is the safe first look at each.

*No corpus evidence — derived from upstream documentation.*
