---
name: "python-keylogger"
categories:
  - blackarch-keylogger
version: "2.7.3"
homepage: "https://github.com/GiacomoLaw/Keylogger"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "GiacomoLaw/Keylogger"
github-stars: 2457
github-pushed: "2025-08-05"
github-archived: false
github-issues: 24
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# python-keylogger

> Minimal X11 keystroke logger for Linux from the GiacomoLaw Keylogger project.

## What it's for

This is the Linux command-line keylogger distributed as the PyPI `keylogger` package from the GiacomoLaw/Keylogger project (the same repository also carries separate Windows and macOS programs, which are not what this package installs). It hooks the X server via python-xlib (pyxhook) and appends the name of every key pressed to a log file — entries are X11 key names such as `a`, `Return` or `space`, not assembled text, so passwords show up as one line per key. It has no daemon, no network features and no hiding mechanism: it is a bare-bones educational logger that stops when its process is killed. On BlackArch the console script is installed as `python-keylogger` (upstream name: `keylogger`) to avoid clashing with other packages.

## Typical use cases

### Record keystrokes to a chosen log file

```
python-keylogger --log-file ~/output.log
```

Outcome: hooks the keyboard of the current X session and appends one key name per line to `~/output.log` (default `./output.log` in the working directory); stop it with Ctrl+C.

### Start from a clean log and stop with a cancel key

```
python-keylogger --clean-file --log-file ~/output.log --cancel-key q
```

Outcome: deletes any existing log file first, then logs as above; pressing `q` (the cancel key, backtick by default) unhooks the keyboard and exits cleanly, in addition to the Ctrl+C path.

*No corpus evidence — derived from upstream documentation.*
