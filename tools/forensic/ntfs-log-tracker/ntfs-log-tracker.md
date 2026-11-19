---
name: "ntfs-log-tracker"
categories:
  - blackarch-forensic
version: "1.6"
homepage: "https://sites.google.com/site/forensicnote/ntfs-log-tracker"
packaged-by: [blackarch]
blackarch-builddate: "2020-09-08"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "blueangel1275/ntfs-log-tracker"
github-stars: 0
github-pushed: "2015-04-01"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntfs-log-tracker

> Windows tool that decodes the NTFS $LogFile and $UsnJrnl:$J journals into a chronological file-operation timeline, and carves USN records out of raw dumps.

## What it's for

NTFS Log Tracker (from blueangel's ForensicNote site) parses both NTFS journals — $LogFile, the filesystem's transaction log, and $UsnJrnl:$J, the change journal — and renders them as a chronological event timeline: files and directories created, written (resident and non-resident data), deleted, renamed and moved, including file-system tunneling cases. Input is the journal files extracted by an acquisition tool such as EnCase or WinHex; additionally supplying $MFT enables full-path resolution for the events, and times are reported in an adjustable timezone. Beyond parsing intact journals it carves USN records from raw sources — unallocated-space dumps, file slack, pagefile.sys, memory dumps, volume shadow copies. Later versions than the 1.6 packaged here add CSV export, $LogFile record carving for damaged filesystems, and pattern-based timestamp-manipulation detection. For $LogFile/USN analysis from Linux with scriptable CLIs, see [[lfle]] and [[extractusnjrnl]].

## No verifiable command-line reference

The tool is distributed as Windows binaries from the author's site with no published argument reference — the upstream page documents behavior (inputs, event types, output) but not invocation syntax — so no example commands are given here. The BlackArch package ships the command-line build of v1.6 (plus sqlite3.dll) under `/usr/share/windows/` behind a Wine launcher, `/usr/bin/ntfs-log-tracker`, which passes its arguments through to the executable under Wine.

*No corpus evidence — derived from upstream documentation.*
