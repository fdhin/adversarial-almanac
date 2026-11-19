---
name: "windows-privesc-check"
categories:
  - blackarch-windows
version: "181.9f304fd"
homepage: "https://github.com/pentestmonkey/windows-privesc-check"
packaged-by: [blackarch, kali]
blackarch-builddate: "2024-12-26"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "pentestmonkey/windows-privesc-check"
github-stars: 1504
github-pushed: "2023-08-01"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# windows-privesc-check

> Standalone Windows executable that audits local systems for privilege-escalation misconfigurations.

## What it's for

windows-privesc-check (pentestmonkey) is a self-contained Windows executable (Python packed with PyInstaller — `windows-privesc-check2.exe`) that hunts for misconfigurations letting an unprivileged user escalate: weak security descriptors on services, program files and registry keys, PATH hijacking, writable startup entries, FAT-filesystem modifiable binaries and similar "weak ACL" classes. It runs best as Administrator (full read access to all securable objects) but deliberately also works as a low-privileged user, doing what it can with reduced rights, and its raw dump mode doubles as a remote enumeration tool for shares, users and groups over SMB. Reports come out in HTML, text and XML. It is a slow, disk/CPU-hungry auditor, not an exploiter — it finds and explains vectors, you act on them. Development stopped around 2015-era Windows, so pair it with newer checkers on modern hosts.

## Typical use cases

### Full local audit with a report

The standard run from an elevated shell on the host being assessed.

```
windows-privesc-check2.exe --audit -a -o wpc-report
```

Outcome: runs all simple checks and writes `wpc-report.html` (recommended), `wpc-report.txt` and `wpc-report.xml` in the current directory — the HTML report lists identified escalation vectors plus appendices of supporting data. The same command works unelevated, with fewer checks succeeding.

### Dump raw auditing data for offline analysis

Skip issue detection and capture the underlying data as a snapshot.

```
windows-privesc-check2.exe --dump -a > dump.txt
```

Outcome: writes extremely verbose human-readable data about securable objects and security settings to `dump.txt`; use `--dumptab -a > dump.tsv` for the tab-delimited machine-readable form.

### Enumerate a remote system's users

From a Windows foothold, list accounts on another host with valid credentials.

```
windows-privesc-check2.exe --dump -U -s 198.51.100.10 -u user -p Password1 -d CORP
```

Outcome: prints the remote system's user list over SMB (`-U`); swap in `-G` for group memberships, `-H` for shares (including permissions), or `-L` for logged-on users (admin rights required for `-L`).

*No corpus evidence — derived from upstream documentation (pentestmonkey README and docs/QuickStartUsage.md).*
