---
name: "proxystrike"
gentoo-category: "net-proxy"
version: "9999"
homepage: "http://code.google.com/p/proxystrike/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "bincker/proxystrike"
github-stars: 6
github-pushed: "2015-05-09"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# proxystrike

> Active web-application proxy by Carlos del Ojo ("deepbit", S21sec): browse a target through it and it attacks every intercepted parameter in the background with SQL injection and XSS plugins.

## What it's for

ProxyStrike (v2.1/2.2, late 2000s, Google Code) is a Python proxy that behaves passively in your browser — you simply surf the target application through it — while in the background it actively attacks every parameter it sees. Its plugin engine carries a SQL injection engine (a Python port of DarkRaver's sqlibf) and an XSS engine, plus an automatic crawler, request interceptor, repeater and diffing, parameter/value statistics, session save/restore, HTML/XML export of results, and support for chaining through an alternate outgoing proxy such as Tor. It is a PyQt4-era Python 2 tool: expect dependency work before it runs on a current interpreter (the Pentoo ebuild wraps it as `proxystrike-cli`/`proxystrike-gui`); for modern workflows an intercepting proxy with active scanning is the equivalent. It is primarily of historical interest as an early "passive for the user, active underneath" web proxy.

## Typical use cases

### Launch the proxy and browse the target through it

```
python proxystrike.py
```

Outcome: the PyQt4 GUI starts and a proxy listens on port 8008; set the browser's HTTP proxy to that port and browse the target application — intercepted parameters are attacked in the background while the application behaves normally, and findings accumulate in the history and attack logs.

### Run the same engine headless

```
python proxystrike.py -c
```

Outcome: ProxyStrike runs its interception and attack engine in console mode without the Qt GUI (the Pentoo package exposes this as the `proxystrike-cli` wrapper), suited to hosts without a display.

*No corpus evidence — derived from upstream documentation.*
