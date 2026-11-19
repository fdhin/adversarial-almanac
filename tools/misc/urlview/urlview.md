---
name: "urlview"
categories:
  - blackarch-misc
version: "1e"
homepage: "http://packages.qa.debian.org/u/urlview.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-04-23"
build-system: make
license: 0BSD, GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "sigpipe/urlview"
github-stars: 154
github-pushed: "2021-12-08"
github-archived: false
github-issues: 19
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# urlview

> Curses menu that extracts URLs from text and launches the one you pick.

*No corpus evidence — derived from upstream documentation.*

## What it's for

urlview (Michael Elkins; classic Debian tool, maintained upstream at
sigpipe) reads text files or stdin, extracts every URL matching its regular
expression, and shows them in a scrollable curses menu — press Enter and
the chosen URL is handed to a launcher command (browser, text browser, or a
handler script). It is the long-standing terminal companion for reading
mail or logs: no more copy-pasting long URLs out of a pager. It extracts
and launches only — it does not fetch or preview anything itself.

## Typical use cases

### Pick a URL out of a text file

```
urlview report.txt
```

Outcome: full-screen menu listing every URL found in the file; move with
the arrow keys and press Enter — the selected URL is passed to the
configured COMMAND (default `/etc/urlview/url_handler.sh`); press `q` to
quit without launching.

### Pipe a message straight into the menu

Use it from mutt, tin, or any pipeline.

```
cat message.txt | urlview
```

Outcome: with no file arguments urlview reads stdin and opens the same
selection menu — this is what mutt's `macro pager \cb |urlview` binds to
Ctrl-b while reading mail.

### Choose the launcher per session

Override the default handler without touching the config file.

```
BROWSER=firefox urlview report.txt
```

Outcome: opens the URL you select with firefox (a colon-separated list is
tried in order) — honored only while no `COMMAND` line is set in
`~/.urlview`; for a permanent setting put `COMMAND firefox %s` (and
optionally a custom `REGEXP`) in `~/.urlview` instead.
