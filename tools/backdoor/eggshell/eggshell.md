---
name: "eggshell"
categories:
  - blackarch-backdoor
version: "157.eaeeea7"
homepage: "https://github.com/neoneggplant/EggShell"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "neoneggplant/EggShell"
github-stars: 1776
github-pushed: "2021-03-25"
github-archived: false
github-issues: 56
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eggshell

> Menu-driven post-exploitation surveillance tool for iOS, macOS and Linux — per-platform payloads, SSL-secured sessions and a multihandler, all from one Python 2.7 console.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Operating machines you already control. Once a payload has run on a target, EggShell gives you an interactive session with file upload/download, tab completion, screenshots, camera and microphone capture, persistence and password retrieval — plus iOS-specific commands (passcode retrieval, SMS/contacts/notes download, location, dial, vibrate) and macOS-specific ones (Facebook session-cookie theft, LaZagne password dumping, iMessage, keyboard simulation). Payloads range from a bash one-liner to Teensy USB-injection sketches for macOS. It is a post-exploitation tool, not an exploit: something must execute your payload first, and upstream bills it as a proof of concept intended for machines you own. Requires Python 2.7.

## Typical use cases

### Start the listener and generate a payload

```
python eggshell.py
```

Outcome: launches the banner menu — the first option starts a single listener, the second the multihandler — and the payload menu produces (among others) a bash one-liner that opens an SSL session back to your listener when run on the target. Typing `help` inside a session lists the platform-specific commands available (`picture`, `mic`, `persistence`, `getpasscode`, `locate`, `lazagne`, ...).

### Run the console with debug output

```
python eggshell.py debug
```

Outcome: same menus, but the server runs with debug enabled and prints "Debug On" plus diagnostic output alongside session handling — use it when a callback is not landing and you need to see what the server is doing.
