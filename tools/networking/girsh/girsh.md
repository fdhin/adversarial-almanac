---
name: "girsh"
categories:
  - blackarch-networking
version: "v0.41.r0.gfc5b44e"
homepage: "https://github.com/nodauf/Girsh"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-05-21"
build-system: go
license: GPL2
meta-checked: "2026-11-09"
github-repo: "nodauf/Girsh"
github-stars: 358
github-pushed: "2023-04-15"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# girsh

> Reverse-shell handler that upgrades itself: generates the one-liner, listens, and makes the shell fully interactive.

## What it's for

Girsh (Golang Interactive Reverse SHell, by nodauf) removes the manual
ritual of upgrading a dumb reverse shell. Run it and it offers
ready-to-paste reverse-shell one-liners (Linux and Windows, via
shellerator), starts the listener, detects the OS of whatever connects,
and fires the right upgrade automatically: python pty spawn plus
`stty raw -echo` and terminal sizing on Linux, AMSI-bypassing ConPTY on
Windows. It is only the handler — you still have to get the one-liner
executed on the target yourself — and it manages one listener at a time.

## Typical use cases

### Spawn a fully interactive session

One command: menu, listener and automatic upgrade.

```
girsh
```

Outcome: prints reverse-shell one-liners for the victim, listens on the
default port 1234, and when a payload connects returns a fully interactive
shell — TTY sizing and raw mode handled on Linux, ConPTY on Windows;
choose a one-liner from the menu and run it on the target.

### Listen on a custom port without the menu

Plain listener mode that still upgrades what connects.

```
girsh listen -p 4444
```

Outcome: listens on TCP/4444 and applies the same automatic Linux/Windows
shell upgrades to the incoming connection; add `-d` for debug output of
the upgrade steps.

*No corpus evidence — derived from upstream documentation.*
