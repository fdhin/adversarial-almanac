---
name: "jshell"
categories:
  - blackarch-webapp
version: "7.ee3c92d"
homepage: "https://github.com/s0md3v/JShell"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "s0md3v/JShell"
github-stars: 531
github-pushed: "2019-05-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jshell

> Turns a reflected XSS into an interactive JavaScript shell in the victim's browser.

## What it's for

JShell (by s0md3v) is a one-command XSS payload handler: it starts a listener, prints a `<script src=...>` payload pointing back at it, and when the payload runs in a victim's browser you get an interactive prompt where every line you type is executed as JavaScript in that page's origin — reading cookies and DOM, issuing same-origin fetches, and so on. It is built on the payload technique demonstrated by Rodolfo Assis (brutelogic). It targets reflected XSS you have already found; it is not a scanner or an injector. The default listener port is 33 and the local IP is auto-detected at startup.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Catch a JavaScript shell from an XSS

Start the handler and deliver its payload.

```
jshell
```

Outcome: the tool auto-detects your IP, listens on port 33, and prints a ready-made script-tag payload; deliver that payload through your reflected-XSS vector, and as soon as the injected page loads you drop into a shell prompt where each line executes in the victim's browser and its output is returned to you.
