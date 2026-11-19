---
name: "sysinternals-suite"
categories:
  - blackarch-windows
version: "6.4"
homepage: "http://sysinternals.com/"
packaged-by: [blackarch]
blackarch-builddate: "2024-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sysinternals-suite

> Microsoft's bundled collection of Windows administration, diagnostics and security utilities.

## What it's for

The Sysinternals Suite is Microsoft's hub of ~80 standalone Windows executables covering process analysis, file and registry inspection, networking, account-privilege auditing and system monitoring. For security work the most-used members are `accesschk` (effective-permission checks on securable objects), `Process Explorer`/`Process Monitor` (live process and system-activity analysis), `Autoruns` (persistence enumeration), `ProcDump` (memory capture), `PsExec` (remote execution), `strings`/`sigcheck` (static triage), `Sysmon` (logging) and `TCPView` (per-process connections). Each tool is a separate binary with its own options — this note covers the common enumeration patterns; individual utilities with their own vault notes (e.g. [[handle]]) are documented there. The suite is distributed from Microsoft's Sysinternals site; there is no official GitHub repository.

## Typical use cases

### Find user-writable paths for privilege escalation

Sweep an entire directory tree for objects the given group can write to — the classic first step when hunting weak service binaries or hijackable paths.

```
accesschk64.exe -w -s -q -u Users "C:\Windows" -accepteula
```

Outcome: prints every file, directory and registry-like object under `C:\Windows` that the `Users` group can modify, recursively and quietly; `-accepteula` suppresses the first-run dialog so it works from an unattended console. Redirect to a file (`>> writable.txt`) to review offline.

### Check what one specific account can write

Same check narrowed to a single principal, useful to confirm a credential you hold gives a write primitive.

```
accesschk64.exe -w -u -s user C:\ -accepteula
```

Outcome: lists only paths writable by `user` across the whole `C:\` drive, revealing exploitable binaries, config files or directories.

### Run a command on a remote host with explicit credentials

PsExec executes a process on a remote Windows system over the admin share, the pattern behind many lateral-movement workflows.

```
PsExec.exe \\198.51.100.10 -u user -p Password1 cmd
```

Outcome: opens an interactive shell running on the remote host under the supplied account; replace `cmd` with any executable path plus arguments for non-interactive runs.

*Corpus evidence: accesschk64.exe permission sweeps (`-w -s -q -u ... -accepteula`) and PsExec `\\host -u user -p pass cmd` invocations; tool semantics verified against Microsoft's Sysinternals documentation.*
