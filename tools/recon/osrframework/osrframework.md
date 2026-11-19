---
name: "osrframework"
categories:
  - identify
version: "0.20.5-0kali3"
homepage: "https://pypi.org/project/osrframework/"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: python
meta-checked: "2026-11-09"
github-repo: "marrocamp/osrframework"
github-stars: 1
github-pushed: "2019-11-21"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# osrframework

> OSRFramework — the i3visio project's library suite and CLI utilities for OSINT: username checks across platforms, email and search-engine pivots, wrapped in an AGPL Python package.

## What it's for

OSRFramework ("Open Sources Research Framework", by Carlos García / i3visio) is a set of Python libraries for open-source intelligence tasks: username checking across many platforms, DNS lookups, information-leak research, deep-web search and regex extraction, usable programmatically or through its installed command-line apps. The PyPI package (last release 0.20.5, also what Kali ships) installs the apps onto your `PATH`: `usufy` checks whether a username exists on given platforms, `searchfy` pivots through search engines, `mailfy` researches email addresses, and `osrf` is the interactive console front-end. Default behavior is configured in `~/.config/OSRFramework/` on Linux/macOS, where API credentials can be added. It is AGPLv3+ software from the mid-2010s Python 3 era: many of the wrapped sites and APIs have changed or died since the last release, so expect some plugins to fail. The canonical upstream `github.com/i3visio/osrframework` (still listed as home by PyPI) has been deleted from GitHub; this note's repository field points at a surviving same-project fork.

## Typical use cases

### Check a username across platforms

The classic usufy pivot: see which accounts exist for one handle.

```
usufy -n user -p twitter facebook
```

Outcome: usufy queries each requested platform for the username and prints a table of results (platform, URL, found or not); add more platform names to widen the sweep, and `-h` lists the full set of options.

### Research an email address

Run the mailfy pivot on a target address's local part.

```
mailfy -n user
```

Outcome: mailfy looks up `user` across the mail/leak services it wraps and reports what it finds, giving you account-existence and breach-style pivots from a single handle.

### Pivot through search engines

Let searchfy gather mentions of an identifier.

```
searchfy -q "user"
```

Outcome: runs the query through OSRFramework's search wrappers and prints the matching results, useful for cross-referencing a nickname that may not map cleanly to platform usernames.

### Explore the console and configuration

Enter the interactive console and check the app's own help.

```
osrf --help
```

Outcome: prints the console's options; inside `osrf` you get the same lookups interactively, and per-app defaults live under `~/.config/OSRFramework/` (restorable from its `defaults` subfolder if a config edit breaks things).

> No corpus evidence — derived from upstream documentation (PyPI package and surviving same-project fork; canonical i3visio repository deleted).
