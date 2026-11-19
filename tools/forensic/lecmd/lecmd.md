---
name: "lecmd"
categories:
  - forensic
homepage: "https://github.com/EricZimmerman/LECmd"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
meta-checked: "2026-11-13"
github-repo: "EricZimmerman/LECmd"
github-stars: 350
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# lecmd

> LECmd (LNK Explorer Command line) — parses Windows `.lnk` shortcuts end to end: target paths and timestamps, shell-item ID lists, tracker database/MAC data, and every extra data block, to console, CSV, JSON, XML or HTML.

## What it's for

Every Windows shortcut is a miniature forensic report: what it points to, when, from which machine and MAC address, and the environment it was created in. LECmd is Eric Zimmerman's reference parser for those files — part of the standard DFIR toolset alongside PECmd and RECmd. Given one `.lnk` or a whole directory, it extracts the target path and its timestamps, reconstructs the full absolute path from the target ID list's shell items (with real folder names where Windows stores localized resource strings), resolves volume/network-share information and the four tracker droids (Volume/File Droid and their birth counterparts — machine ID and MAC traces), and dumps every extra data block including anomalous or damaged structures, which it reports rather than chokes on. Use it in investigations to map user activity: which USB drives were attached, which documents were opened, and which systems created the shortcuts. It is a parser and reporter only — it does not modify evidence, and it is not a shortcut editor.

## Typical use cases

### Examine a single shortcut in full

```
LECmd.exe -f "C:\Temp\foobar.lnk"
```

Outcome: every field of the shortcut prints to the console — target path and timestamps, the complete target ID list (shell items), volume/tracker data with machine and MAC, and all extra blocks; `--nid`/`--neb` trim the ID list or extra blocks from the display when you only want the header facts.

### Triage a whole evidence directory

```
LECmd.exe -d "C:\Temp" --csv "c:\temp\out" --html c:\temp\out --xml c:\temp\out\xml -q
```

Outcome: every `*.lnk` under the directory (recursively) is parsed; CSV gets one row per shortcut (including `TargetIDAbsolutePath` and the `Anomalies` column), HTML/XML get one report file per shortcut, and `-q` suppresses the per-file console detail so large runs stay readable; `--all` extends processing to non-`.lnk` files in the directory.

### Export structured data for timeline tooling

```
LECmd.exe -f "C:\case\link.lnk" --json "D:\jsonOutput" --pretty
```

Outcome: one JSON object per shortcut with all timestamps normalized to ISO 8601 UTC (`2024-01-02T03:04:05.6780000Z`) — the shape timeline ingestors and spreadsheet pivots want; `--mp` adds sub-second precision and `--dt` reformats console timestamps.

### Focus on removable-media activity

```
LECmd.exe -d "C:\Users\user\Desktop" -r
```

Outcome: only shortcuts whose target ID list points at removable drives are processed — a quick map of USB sticks used on the workstation, with their volume droids and serial numbers available for correlation against other artifacts.

*No corpus evidence — derived from upstream documentation.*
