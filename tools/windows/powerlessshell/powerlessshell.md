---
name: "powerlessshell"
categories:
  - blackarch-windows
version: "117.b168ccb"
homepage: "https://github.com/Mr-Un1k0d3r/PowerLessShell"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-12"
build-system: windows
license: GPL
meta-checked: "2026-11-09"
github-repo: "Mr-Un1k0d3r/PowerLessShell"
github-stars: 1562
github-pushed: "2026-04-09"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# powerlessshell

> PowerLessShell — wrap a PowerShell script or raw shellcode in a Visual Studio project file so MSBuild.exe executes it without powershell.exe ever appearing.

## What it's for

PowerLessShell (Mr.Un1k0d3r, RingZer0 Team; python3 port by @whokilleddb) is a generator, not an exploit: it takes a PowerShell script or raw shellcode and produces a `.csproj` MSBuild project that embeds it. On the target, `MSBuild.exe` — a signed Microsoft binary that application-control rules rarely block — compiles and runs the project, which hosts a PowerShell runspace internally (System.Management.Automation) or injects the shellcode: no powershell.exe process is spawned, and nothing beyond the project file touches disk. The generator side is a Python script (`PowerLessShell.py`), so it runs from any attacker box; the output file is what you deliver. It also supports MSBuild `<Target>` conditions, so the payload can be made to fire only on machines matching a property (user domain, registry value), and a companion Cobalt Strike Aggressor script (`wmi_msbuild.cna`) drives remote MSBuild execution over WMI. It does not provide the initial access — you still need a way to drop the `.csproj` and invoke MSBuild.

## Typical use cases

### Wrap a PowerShell script

The primary form: script in, MSBuild project out.

```
python3 PowerLessShell.py -type powershell -source script.ps1 -output malicious.csproj
```

Outcome: reads `script.ps1`, embeds it in `include/template-powershell.csproj`, and writes `malicious.csproj` (`File 'malicious.csproj' created`). Executed by MSBuild on a Windows host, it runs the script inside an in-process PowerShell runspace.

### Wrap raw shellcode, choosing the architecture

The shellcode form, for position-independent payloads.

```
python3 PowerLessShell.py -source shellcode.raw -output malicious.csproj -arch 64
```

Outcome: writes a project that executes the raw shellcode bytes; `-arch 64` selects the 64-bit template (default is 32) — pair it with the matching MSBuild (`Framework64` on x64 Windows) or execution fails.

### Gate execution on a target property

Make the payload refuse to run on machines that don't match.

```
python3 PowerLessShell.py -type powershell -source script.ps1 -output malicious.csproj -condition "'\$(USERDOMAIN)'=='CORP'"
```

Outcome: the generated project carries an MSBuild condition (default checks USERDOMAIN) so the malicious target only compiles when the condition holds — e.g. domain-joined CORP machines — keeping sandboxes and analysts' boxes silent. Conditions can also probe registry values and MSBuild property functions.

### Execute the project on the target

The delivery step the generator sets up.

```
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe malicious.csproj
```

Outcome: MSBuild compiles the project in-memory and the payload runs under the MSBuild process — no powershell.exe, and on the WMI path (via the bundled Aggressor script) reachable remotely with credentials. Verify .NET 4.0.30319 is present on the target first (the Aggressor script's `check_msbuild` exists for exactly this).

*No corpus evidence — derived from upstream documentation.*
