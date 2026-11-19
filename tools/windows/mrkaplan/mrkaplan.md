---
name: "mrkaplan"
categories:
  - blackarch-windows
version: "1.1.1"
homepage: "https://github.com/Idov31/MrKaplan"
packaged-by: [blackarch]
blackarch-builddate: "2023-04-12"
build-system: windows
license: BSD
meta-checked: "2026-11-09"
github-repo: "Idov31/MrKaplan"
github-stars: 275
github-pushed: "2023-09-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mrkaplan

> PowerShell anti-forensics helper for red teamers: snapshots execution artifacts before an operation and wipes or restores them on the way out.

## What it's for

MrKaplan (Idov31) helps red teamers stay hidden by clearing evidence of execution. Run with `begin` before operating on a Windows host, it records the state it needs (time it ran, file snapshots, per-user associations) under `HKCU:\Software\MrKaplan`; run with `end` afterwards, it reverts what the operation left behind. It is inspired by MoonWalk and is PowerShell-only, so it runs anywhere a modern Windows box does — although it is most effective as admin, since it can then stop event logging and clean artifacts for multiple users.

What `end` cleans spans three modules: event logging (ETW bypass, selectable method), file artifacts (prefetch, INet cache, Windows/Office history folders) and registry artifacts (PowerShell history, UserAssist, BAM, ComDlg32, AppCompatCache). It is deliberately selective — individual artifact classes can be excluded from cleanup. The packaged BlackArch release is the standalone `MrKaplan_Standalone.ps1` script; the repo ships the modular `MrKaplan.ps1` plus the same code.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Snapshot the host before operating

```
.\MrKaplan.ps1 begin
```

Outcome: creates the cleanup configuration under `HKCU:\Software\MrKaplan` (file snapshots, timestamps, affected users) so a later `end` can restore the pre-operation state. Run this as admin before touching the host; do not delete the registry key or `MrKaplan-Config.json` until `end` has run, or the cleanup information is lost.

### Cover several users and deliberately leave some artifacts

```
.\MrKaplan.ps1 begin -Users user,admin -Exclusions Prefetch,Eventlogs
```

Outcome: tracks cleanup for the named users (in addition to the current one) while leaving prefetch and event logs untouched — useful when full wiping would itself raise a blue-team alert. Other runtime options: `-RunAsUser` to clean with user privileges only, and `-EtwBypassMethod overflow` to pick how event logging is stopped.

### Wipe the traces on the way out

```
.\MrKaplan.ps1 end
```

Outcome: stops event logging, overwrites PowerShell history, and clears the recorded registry artifacts (UserAssist, BAM, ComDlg32, AppCompatCache), prefetch and history folders for every tracked user, reverting the host toward its pre-`begin` state.

### Preserve a file's original timestamps

```
.\MrKaplan.ps1 timestomp -StompedFilePath C:\path\to\file
```

Outcome: saves the file's timestamps so `end` can restore them after your tooling has modified the file's times — record this before timestomping anything during the operation.
