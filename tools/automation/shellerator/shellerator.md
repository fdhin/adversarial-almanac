---
name: "shellerator"
categories:
  - blackarch-automation
version: "72.6af7f36"
homepage: "https://github.com/ShutdownRepo/shellerator"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-29"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ShutdownRepo/shellerator"
github-stars: 392
github-pushed: "2026-06-16"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 37
gh-activity-checked: "2026-11-09"
---

# shellerator

> CLI/TUI generator for one-liner reverse, bind and web shells across many languages (Bash, PowerShell, Netcat, PHP, ASPX, Python, ...) — no cheatsheet tab-hopping mid-engagement.

## What it's for

Producing a copy-pasteable shell one-liner for exactly the language and direction you need. You tell it reverse, bind or web shell, the shell type, and your listener IP/port; it prints the command (built from the same curated sources as the classic reverse-shell cheatsheets, kept as a maintained Python project). Two interaction modes: fully non-interactive with flags, or a terminal UI that walks you through the choices whenever a required option is omitted — useful when you want to browse what exists. It generates payloads only: it does not start listeners for you (combine it with your own `nc -lvnp`), and it does not encode, stage or obfuscate what it emits. Actively maintained by the ShutdownRepo project and installed by default in the Exegol pentest environment.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate a reverse shell non-interactively

```
shellerator -r -t bash -lh ATTACKER_IP -lp 4444
```

Outcome: prints a ready-to-paste Bash reverse shell one-liner connecting back to ATTACKER_IP:4444. `-r` is the default direction (so it can be omitted), `-t` selects the language/shell type, and `-lh`/`-lp` set where the target should call back to. Start your listener (`nc -lvnp 4444`) before pasting the output on the target.

### Generate a bind shell on a chosen port

```
shellerator -b -t netcat -lp 1337
```

Outcome: prints a one-liner that opens a listener on port 1337 on the target; you then connect out to it with `nc TARGET 1337`. Bind mode needs no `--lhost` since the target serves the connection. Add `-v` for verbose output about what is generated.

### Generate a webshell payload

```
shellerator -w -t php
```

Outcome: prints the source of a PHP webshell to drop on a web-exposed host (ASPX and other web languages available — run `shellerator -l` first to list every supported shell type per mode). The output is the file content to save, not a command to run.

### Browse options through the TUI

```
shellerator
```

Outcome: omitting required options (here, all of them) drops you into the interactive terminal UI, which prompts for direction (reverse/bind/web), shell type and listener details in order, then prints the payload — the fastest way to discover which types exist for a language without memorizing the `-t` values (`--list` prints the same catalog non-interactively).
