---
name: "enabler"
categories:
  - blackarch-cracker
version: "1"
homepage: "http://packetstormsecurity.org/cisco/enabler.c"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
aliases: ["cisco-bruteforce-enabler"]
meta-checked: "2026-11-09"
github-repo: "Seabreg/Cisco_Enabler"
github-stars: 0
github-pushed: "2019-05-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# enabler

> Telnet brute-forcer for the Cisco enable (privileged-mode) password, working from a known VTY login.

*No corpus evidence — derived from upstream documentation.*

## What it's for

enabler (norby / Avatar Corp, single C file from the PacketStorm era) targets one specific gap: you already have (or guessed) a Cisco router's VTY login, but `enable` guards privileged mode with a separate password that gets no lockout. The tool telnets in, handles both password-only and username/password login prompts automatically, then feeds every line of a wordlist to the `Password:` prompt of `enable` until one is accepted. It speaks only the telnet CLI of IOS-style devices on port 23 (or a port you give) — it does not attack SSH, HTTP management interfaces, or any non-Cisco prompt, and it has no throttling intelligence: it relies on the router not rate-limiting failed enables.

GitHub: third-party mirror of the PacketStorm enabler.c (norby, Avatar Corp).

## Typical use cases

### Brute-force enable with a password-only login

The router asks only for a password at VTY login.

```
enabler 198.51.100.10 cisco enable-candidates.txt
```

Outcome: connects to 198.51.100.10 on port 23, logs in with the known VTY password `cisco`, then tries each entry of `enable-candidates.txt` as the enable password, printing progress per attempt; when one is accepted the privileged prompt is reached and the working password is reported. Ctrl-C exits with a count of passwords tried.

### Brute-force enable with a username/password login

The VTY requires a username as well.

```
enabler 198.51.100.10 -u admin cisco enable-candidates.txt
```

Outcome: same flow, but the tool first answers the username prompt with `admin`, then the login password prompt with `cisco`, then runs the enable list. It auto-detects which prompt style the device presents (`login:`/`Username:` vs `Password:` only).

### Run against a non-default telnet port

Rotated VTY port on the target.

```
enabler 198.51.100.10 cisco enable-candidates.txt 2323
```

Outcome: identical attack against port 2323 instead of 23 — useful where the management interface was moved off the standard telnet port.
