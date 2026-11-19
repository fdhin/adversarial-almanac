---
name: "fakemail"
categories:
  - blackarch-misc
version: "1.0"
homepage: "http://sourceforge.net/projects/fakemail/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "gma/fakemail"
github-match: "exact"
github-stars: 0
github-pushed: "2024-08-12"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fakemail

> Minimal SMTP server that writes every message it accepts to a file — for acceptance testing without a real mail system.

*No corpus evidence — derived from upstream documentation.*

## What it's for

fakemail (the Python implementation by Graham Ashton, from the SourceForge fakemail project) is a throwaway mail server for software testing. It implements just enough SMTP for an application under test to "send" mail, and stores each accepted message as a file in a directory — sparing you a real MTA and mail-queue forensics when all you want is to assert what the app sent. Defaults: binds to localhost, port 8025, writes messages to the current working directory; all three are overridable. It never relays or delivers anywhere — capture only, so it is safe to point staging apps at. It is a Python 2-era tool (an original Perl implementation exists on the same project site); the packaged command is `fakemail`.

## Typical use cases

### Capture mail from an app under test

Point the application's SMTP settings at fakemail and run the scenario.

```
fakemail --path ./outbox
```

Outcome: listens on localhost:8025; every message the app sends is written as its own file under `./outbox` — open the files to assert recipients, subjects, and bodies without any mail infrastructure.

### Bind a custom address and port

Match the SMTP settings the application or device already has.

```
fakemail --host 198.51.100.10 --port 25 --path ./outbox
```

Outcome: serves SMTP on the given address and port (useful when the client is another host or hardcodes port 25) and stores captured messages in `./outbox` as before.

### Run unattended with a log

Keep fakemail out of the way during long test runs.

```
fakemail --background --log ./fakemail.log --path ./outbox
```

Outcome: detaches into the background (double-fork daemon; unsupported on Windows, where it prints a notice instead) and appends progress messages to `./fakemail.log`, while message files still land in `./outbox`.
