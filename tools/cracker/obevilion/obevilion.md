---
name: "obevilion"
categories:
  - blackarch-cracker
version: "409.29fbe9d"
homepage: "https://github.com/BL4CKvGHOST/Ob3vil1on"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "BL4CKvGHOST/Ob3vil1on"
github-stars: 23
github-pushed: "2018-08-07"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# obevilion

> Python 2 brute-force cracker for password-protected ZIP, 7z and RAR archives, with CLI, interactive and Qt GUI front-ends.

## What it's for

Ob3vil1on (2017, archived upstream) recovers passwords of encrypted ZIP, 7z and RAR archives by brute force: point it at a file, it detects the archive type automatically and iterates candidate passwords until one opens the archive, delegating the actual extraction to the unrar/p7zip utilities on the host. It is a small Python 2 / PyQt 4 era project — the README asks that the script be run as root, and lists a dictionary mode (`-d`) in the CLI even though dictionary attacks are documented as not yet implemented; brute force (`-b`) is the working mode. Modern hashcat and John the Ripper archive modes cover the same ground without a Python 2 runtime.

## Typical use cases

### Brute-force an archive password

Point the CLI at a protected archive.

```
obevilion --cli -b archive.zip
```

Outcome: the archive type (zip/7z/rar) is detected from the file itself and candidate passwords are generated and tried until one succeeds; the recovered password is printed. The upstream README requires running the script as root.

### Interactive prompt mode

Guided session without memorizing flags.

```
obevilion --easy_mode
```

Outcome: starts an interactive menu session (`+=> help` lists the available commands) that walks through choosing the archive and attack parameters step by step.

### GUI session

Same attacks from a Qt window.

```
obevilion --gui
```

Outcome: opens the Ob3vil1on PyQt 4 GUI where the archive and attack options are selected with the mouse; the packaged build needs a working Python 2 PyQt 4 stack installed.

*No corpus evidence — derived from upstream documentation.*
