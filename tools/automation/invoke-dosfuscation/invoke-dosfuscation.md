---
name: "invoke-dosfuscation"
categories:
  - blackarch-automation
version: "7.6260f5b"
homepage: "https://github.com/danielbohannon/Invoke-DOSfuscation"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-24"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "danielbohannon/Invoke-DOSfuscation"
github-stars: 956
github-pushed: "2018-03-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# invoke-dosfuscation

> cmd.exe command obfuscation framework with a built-in detection test harness.

## What it's for

Invoke-DOSfuscation (Daniel Bohannon) generates randomly obfuscated Windows `cmd.exe` commands — string concatenation, command reversing, FOR-loop encoding, and FIN7-style environment-variable substitution — so defenders can generate thousands of unique variants and test whether their command-line detections (Security EID 4688, Sysmon EID 1) catch them. Its documented purpose is blue-team detection tuning: it ships a fuzzing test harness, a regex-based detection matcher, and 1000 sample obfuscated commands with matching Security/Sysmon event logs. It is not a payload generator — it transforms a command you supply.

## Typical use cases

### Launch the interactive obfuscation console

Start the module and menu wrapper.

```
Import-Module .\Invoke-DOSfuscation.psd1
Invoke-DOSfuscation
```

Outcome: opens the menu-driven console; enter a cmd.exe command, choose one of the four obfuscation families and a level 1-3, and the console prints the obfuscated result plus the process argument tree for detection work.

### Obfuscate a command directly with a standalone function

Use one of the four obfuscation functions outside the menu wrapper.

```
'netstat -ano' | Out-DosConcatenatedCommand -ObfuscationLevel 3
```

Outcome: emits a command line that still executes `netstat -ano` but with concatenation obfuscation applied at the heaviest of the three preset profiles; higher levels stack more techniques per output.

### Find sample obfuscated commands your detections miss

Check the bundled static samples against your detection regexes.

```
(Get-Content .\Samples\STATIC_1-of-4_Out-DosConcatenatedCommand.txt) | where-object { -not (Get-DosDetectionMatch -Command $_).Detected }
```

Outcome: every line printed is one of the bundled obfuscated variants that none of the regex rules currently configured in `$regexDetectionTerms` detects — a to-do list for coverage gaps.

### Test coverage against recorded process-creation logs

Extract command lines from the bundled Security EID 4688 event log and check each one.

```
(Get-WinEvent -Path .\Samples\DYNAMIC_SECURITY_EID4688_1-of-4_Out-DosConcatenatedCommand.evtx) | where-object { $_.Message } | foreach-object { ($_.Message.Split("`n") | select-string '^\tProcess Command Line:\t') -replace '^\tProcess Command Line:\t','' } | where-object { -not (Get-DosDetectionMatch -Command $_).Detected }
```

Outcome: prints the obfuscated child-process command lines from the sample event log that evade the configured detection rules, so detections can be tuned against realistic logged executions.

*No corpus evidence — derived from upstream documentation.*
