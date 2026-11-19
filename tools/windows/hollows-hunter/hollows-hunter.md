---
name: "hollows-hunter"
categories:
  - blackarch-windows
version: "0.4.1.1"
homepage: "https://github.com/hasherezade/hollows_hunter"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-14"
build-system: windows
license: BSD
meta-checked: "2026-11-09"
github-repo: "hasherezade/hollows_hunter"
github-stars: 2432
github-pushed: "2026-06-06"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 35
gh-activity-checked: "2026-11-09"
---

# hollows-hunter

> Scan every process on a Windows box and dump injected, hollowed or patched code for analysis.

## What it's for

HollowsHunter (hasherezade) is a Windows command-line scanner built on the PE-sieve engine. It inspects live process memory and recognizes the classic implant shapes — replaced/hollowed PEs, injected DLLs and shellcode, inline hooks and in-memory patches — then dumps the suspicious content to disk for offline analysis. Unlike PE-sieve, which scans one PID at a time, HollowsHunter sweeps many processes at once (by default every accessible process) and adds filters (by PID, name or creation time), continuous rescan (`/loop`), an ETW listener mode, and kill/suspend actions on detection. Scanning for hooks and code patches is off by default to reduce noise — enable it with `/hooks`. It detects, reports and dumps; it does not clean or disinfect.

## Typical use cases

### Scan all running processes

Quick triage of a live suspect system.

```
hollows_hunter64.exe
```

Outcome: walks every accessible process, prints a per-process report of implants found, and writes dumps of the suspicious modules/shellcodes into the current directory; `/help` lists all arguments.

### Scan only specific processes by name

Focus on the processes malware typically injects into.

```
hollows_hunter64.exe /pname svchost.exe;explorer.exe
```

Outcome: scans only processes with the given names (separate several with `;`), which is faster and quieter than a full-system sweep and skips dumping noise from unrelated processes.

### Scan by PID

Target exactly the processes you identified as suspicious.

```
hollows_hunter64.exe /pid 456;123
```

Outcome: scans only the listed PIDs (decimal or hexadecimal); combined with `/hooks` it also reports inline hooks and patched code in those processes.

### Keep dumps in timestamped directories

Avoid overwriting evidence between scans.

```
hollows_hunter64.exe /dir C:\dumps /uniqd
```

Outcome: instead of dumping into the working directory, each scan creates a unique timestamped subdirectory under `C:\dumps`, so repeated scans during an incident keep their dumps separated.

### Rescan in a loop while unpacking malware

Catch the moment a packer injects.

```
hollows_hunter64.exe /loop /pname target.exe
```

Outcome: rescans the selected process continuously instead of once, letting you capture the injected payload right after the packer deploys it — hold a debugger or procmon alongside; expect higher CPU use on full-system loops.

### Run as an ETW listener

React to process events instead of polling.

```
hollows_hunter64.exe /etw
```

Outcome: (64-bit build, elevated) starts in ETW listener mode, writes an `HH_ETWProfile.ini` you can edit to select the events to react to, then scans processes as the subscribed events fire — restart after editing the profile.

*No corpus evidence — derived from upstream documentation.*
