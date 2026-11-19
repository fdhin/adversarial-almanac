---
name: "screenshotbof"
categories:
  - scanner
version: "git (2026-07-28)"
homepage: "https://github.com/codextf2/screenshotbof"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "codextf2/screenshotbof"
github-stars: 508
github-pushed: "2026-07-28"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# screenshotbof

> Cobalt Strike screenshot BOF: captures the screen via WinAPI without fork & run and downloads the JPEG in memory.

## What it's for

ScreenshotBOF replaces Cobalt Strike's built-in screenshot post-exploitation job — which uses the heavily monitored fork & run pattern — with an in-process Beacon Object File: it captures with WinAPI (full screen or a specific PID, minimized windows included), compresses to JPEG with configurable grayscale, quality and downscaling, and exfiltrates through the beacon in memory (or writes to disk) without spawning a sacrificial process. Load it by importing `screenshotBOF.cna` into Cobalt Strike's Script Manager; the README notes no evasion is performed beyond the quieter execution model. Windows targets only, as with any BOF.

## Typical use cases

### Capture the full screen and exfiltrate as a screenshot

The OPSEC-quieter replacement for the stock `screenshot` command.

```
screenshot_bof screen.jpg 2 0 0 90 100
```

Outcome: (beacon console, with screenshotBOF.cna loaded) captures the full screen (PID 0), encodes JPEG at quality 90 with no grayscale and 100% scale, and downloads it over the beacon as a screenshot named `screen.jpg` (save method 2); the beacon logs the received screenshot and "Screenshot saved/downloaded successfully".

### Capture a specific — even minimized — window

Grab one application's window by PID.

```
screenshot_bof window.jpg 1 21964 1 80 75
```

Outcome: captures only the window belonging to PID 21964 (works while minimized), converts to grayscale at quality 80 and 75% scale, and downloads it over the beacon as a file (save method 1); save method 0 instead drops the file to disk on the target.

### Pick options interactively

No argument order to remember.

```
screenshot_bof
```

Outcome: with no arguments a GUI dialog pops on the target to choose filename, save method, PID, grayscale, quality and scale, then performs the capture.

*No corpus evidence — derived from upstream documentation (codextf2/screenshotbof README).*
