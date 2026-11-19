---
name: "chromefreak"
categories:
  - blackarch-forensic
version: "24.12745b1"
homepage: "http://osandamalith.github.io/ChromeFreak/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "OsandaMalith/ChromeFreak"
github-stars: 70
github-pushed: "2015-03-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# chromefreak

> Interactive forensic framework that parses the current user's Google Chrome profile — history, downloads, bookmarks, cookies, passwords — from a numbered menu.

## What it's for

ChromeFreak (by Osanda Malith Jayathissa) is a single-script, cross-platform (Windows/Linux/macOS) forensic tool for Google Chrome artifacts. Launched with no arguments, it auto-detects the live Chrome profile directory for the platform (on Linux `~/.config/google-chrome/Default/`, on Windows the `LocalAppData` path, on macOS `~/Library/Application Support/Google/Chrome/Default/`) and presents a numbered menu that parses the corresponding SQLite databases and JSON files, prints the records, and offers to save each result to a file. It examines the running user's own profile rather than an arbitrary evidence directory, so for dead-box analysis copy the profile into place first. It is a 2015-era Python 2 tool; the password investigator is a Windows-oriented capability (it relies on the Windows DPAPI).

## Typical use cases

### Run the interactive investigation menu

```
chromefreak
```

Outcome: clears the screen, prints the banner and the menu — 1 History, 2 Downloads, 3 Bookmarks, 4 Cookies, 5 Full Report, 6 Passwords, 7 Exit. Choosing an item parses the matching artifact from the detected Chrome profile, prints the parsed records (visits, downloaded URLs, bookmark trees, cookies), and asks whether to save the output to a file; option 5 produces a combined report across artifacts.

*No corpus evidence — derived from upstream documentation.*
