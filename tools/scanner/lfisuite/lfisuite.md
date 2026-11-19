---
name: "lfisuite"
categories:
  - blackarch-scanner
version: "85.470e01f"
homepage: "https://github.com/D35m0nd142/LFISuite"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "D35m0nd142/LFISuite"
github-stars: 1974
github-pushed: "2022-04-13"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lfisuite

> Automatic LFI scanner and exploiter with eight attack modes and reverse-shell delivery.

## What it's for

LFISuite scans lists of URLs for local file inclusion and automatically exploits what it finds, using eight attack modalities (php://filter, php://input, /proc/self/environ, /proc/self/fd, access-log poisoning, phpinfo, data://, expect://) plus an Auto-Hack mode that chains all of them per target. A successful LFI shell can be converted into a reverse shell for Windows, Linux and macOS, and Tor proxying is supported. It is a Python 2.7 console application: run it as root the first time and it installs any missing modules itself. For a minimal one-URL probe see [[lfi-scanner]] — an unrelated Perl scanner by a different author.

## Typical use cases

### Scan and exploit automatically

Hand it a list of candidate paths and let it try everything.

```
lfisuite
```

Outcome: launches the interactive menu as root (first run self-installs missing Python modules); choose the scanner and supply a list of paths to test, or select Auto-Hack to run every attack method against each path — bundled small and huge path lists ship with the tool. On success you get an LFI shell prompt; typing `reverseshell` there makes the target connect back to your listener (start one with `nc -lvp <port>`).
