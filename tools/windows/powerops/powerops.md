---
name: "powerops"
categories:
  - blackarch-windows
version: "32.13fe55b"
homepage: "https://github.com/fdiskyou/PowerOPS"
packaged-by: [blackarch]
blackarch-builddate: "2019-03-17"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "fdiskyou/PowerOPS"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# powerops

> PowerOPS — a portable C# runspace console that runs an arsenal of offensive PowerShell modules without ever launching powershell.exe.

## What it's for

PowerOPS (fdiskyou) is a single self-contained Windows executable that hosts a PowerShell runspace via the .NET System.Management.Automation API and exposes an interactive `PS >` prompt — no powershell.exe process, payloads decoded and run from memory — built to slip past GPO, Software Restriction Policies and AppLocker rules that block PowerShell's normal host, and to keep disk clean. The runspace preloads a curated post-exploitation toolkit (PowerSploit's Invoke-Mimikatz, Invoke-DllInjection, Invoke-Shellcode, Invoke-ReflectivePEInjection, Invoke-NinjaCopy, Invoke-TokenManipulation, PowerUp, PowerView; Nishang's Get-Information, Get-PassHashes, Port-Scan; Empire's Invoke-PsExec and Invoke-SSHCommand; plus Auto-GPPPassword, PowerCat, SMBAutoBrute, mimikittenz and PowerUpSQL), and any other valid PowerShell command can be typed at the prompt too. It is inspired by p0wnedShell but deliberately leaner. Two caveats from its own README: on a non-domain-joined machine it warns that domain-oriented functions will not work, and the project is no longer developed — the upstream repository is gone (archived read-only for a while, then deleted), so archived copies of the README are the documentation of record.

## Typical use cases

### List the embedded modules

First move after launching the binary: see what the runspace carries.

```
PowerOPS.exe
PS > show
```

Outcome: starts the interactive runspace console and `show` prints the embedded functions grouped by source toolkit (Nishang, PowerSploit, Empire, Others); on a workgroup machine it first warns `[-] This computer is not part of a Domain! Some functions will not work!`.

### Enumerate a loaded module's functions

Discover everything inside a module such as PowerUp.

```
PS > get-command -module powerup
```

Outcome: PowerShell enumerates the runspace's in-memory modules and lists each function PowerUp provides (Find-DLLHijack, Get-ServiceUnquoted, Invoke-AllChecks, Test-ServiceDaclPermission, ...), exactly as in a normal PowerShell session.

### Run an embedded function at the prompt

Invoke any of the loaded tools — or any valid PowerShell — directly.

```
PS > Invoke-AllChecks
```

Outcome: runs the misconfiguration sweep of PowerUp inside the runspace and prints its findings to the console; the same prompt accepts Invoke-Mimikatz, Invoke-Shellcode or arbitrary PowerShell expressions, all under the PowerOPS process with no powershell.exe spawned.

*No corpus evidence — derived from upstream documentation (archived repository page and README).*
