---
name: "invoke-obfuscation"
categories:
  - blackarch-automation
version: "45.f20e7f8"
homepage: "https://github.com/danielbohannon/Invoke-Obfuscation"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-24"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "danielbohannon/Invoke-Obfuscation"
github-stars: 4342
github-pushed: "2023-08-10"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# invoke-obfuscation

> PowerShell v2.0+ command and script obfuscation framework for testing command-line detections.

## What it's for

Invoke-Obfuscation (Daniel Bohannon) obfuscates PowerShell commands and scripts layer by layer: token-level transforms (variable/string/type/member/argument), string manipulation, encodings (ASCII, hex, octal, binary, BXOR, SecureString), whole-script compression, and AST manipulation — then wraps the result in launchers (powershell, cmd, wmic, mshta, rundll32, CLIP+) that move the command line out of `powershell.exe`'s arguments. Its documented purpose is defensive: generating obfuscated samples to test what survives EID 4688/Sysmon EID 1 command-line logging, and to argue for ScriptBlock/Module/transcription logging. It transforms payloads you supply; it does not create them. Both a menu-driven console and a non-interactive CLI mode exist.

## Typical use cases

### Launch the interactive obfuscation console

Load the module and enter the menu wrapper.

```
Import-Module ./Invoke-Obfuscation.psd1
Invoke-Obfuscation
```

Outcome: opens the interactive console; supply a command or script path, walk the TOKEN / STRING / ENCODING / COMPRESS menus to layer obfuscation, then pick a LAUNCHER — the tool prints the final one-liner, which can be copied straight into a detection-lab VM.

### Obfuscate a scriptblock non-interactively

Reproduce an entire menu walk as one CLI invocation.

```
Invoke-Obfuscation -ScriptBlock {Write-Host 'CLI FTW!'} -Command 'Token\All\1,Encoding\1,Launcher\Stdin++\234,Clip' -Quiet -NoExit
```

Outcome: applies all token transforms, an encoding pass, and the Stdin++ launcher with options 2/3/4, then copies the obfuscated one-liner to the clipboard (`Clip`); `-Quiet` suppresses menu output, `-NoExit` leaves the console open.

### Chain several obfuscation layers with one command string

Type a comma-separated layer list directly at the interactive prompt.

```
Token\All\1,String\3,Encoding\5,Launcher\Ps\234,Clip
```

Outcome: applies token obfuscation, string transform 3, SecureString encoding (option 5) and a powershell launcher in sequence, copying the stacked result to the clipboard — same effect as navigating each menu manually.

### Generate many variants with regex menu syntax

Fan out a layer list over every matching combination.

```
Token\*\*,String\[13],Encoding\(1|6),Launcher\.*[+]{2}\234,Clip
```

Outcome: expands the wildcards and character-class alternatives into every matching transform combination, producing a spread of distinct obfuscation outputs for gauging how broad your detection coverage needs to be.

Corpus usage shows token obfuscation (`Token\All\1`) plus a Stdin++ launcher as one observed recipe — the standard recipe for evading command-line logging while keeping execution in-memory.
