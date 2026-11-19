---
name: "johnny"
categories:
  - blackarch-cracker
version: "20120424"
homepage: "http://openwall.info/wiki/john/johnny"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-07-12"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "openwall/johnny"
github-stars: 648
github-pushed: "2023-09-29"
github-archived: false
github-issues: 21
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# johnny

> Cross-platform Qt GUI front-end for John the Ripper cracking sessions.

## What it's for

Johnny is a desktop GUI for John the Ripper: it wraps hash-format detection, the various attack modes (single, wordlist, incremental, plus raw John options), session management, and password/hash bookkeeping in a point-and-click interface. Reach for it when you want to run, pause, resume and compare cracking sessions without driving John from the command line. It is only a front-end — a John the Ripper installation (core or jumbo) must be present, since John does the actual cracking — and it has no non-interactive/scriptable mode; the whole workflow happens in the GUI. It takes no meaningful command-line arguments beyond the standard Qt ones.

## Typical use cases

### Start a GUI cracking session on a hash file

Launch the application, then drive John from the desktop.

```
johnny
```

Outcome: the main window opens. Open a password/hash dump via the file dialog, let Johnny detect the hash formats, pick an attack mode (or type a raw John command line in the options field), and start the attack — progress, statistics and cracked plaintexts appear live in the session table. Sessions can be paused, resumed and restored later, and several sessions tracked side by side.

*No corpus evidence — derived from upstream documentation.*
