---
name: "swfintruder"
categories:
  - blackarch-reversing
version: "0.9.1"
homepage: "https://code.google.com/archive/p/swfintruder/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "wisec/swfintruder"
github-stars: 6
github-pushed: "2016-08-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# swfintruder

> Browser-based runtime analyzer for Flash (SWF) applications that probes loaded movies for cross-site scripting and cross-site flashing flaws.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SWFIntruder (Stefano Di Paola, 2007, later maintained under the OWASP Flash Security Project) performs security analysis of Flash applications by loading a target SWF inside a browser and exercising it from JavaScript/ActionScript — the methodology described in "Testing Flash Applications". It semi-automates checks for XSS and cross-site flashing in Flash ≤ 8 era movies; it is a runtime analyzer, not a static decompiler. The tool is a static web application (HTML + JavaScript + ActionScript) with no CLI: its documented deployment is to drop the directory on any web server and open it in a browser. It is unmaintained since 2008 and requires a Flash Player ≥ 9 plugin — modern browsers cannot run it unmodified, so treat it as a historical/artifact tool.

## Typical use cases

### Serve the packaged web application locally

BlackArch installs the whole application tree into `/usr/share/swfintruder/`; any static file server turns it into the working tool UI.

```
cd /usr/share/swfintruder && python3 -m http.server 8080
```

Outcome: serves SWFIntruder's `index.html` at `http://127.0.0.1:8080/` — the upstream-documented way to run it ("save the SWFIntruder root directory to your webserver and point your browser to it"). Open it in a Flash-capable legacy browser (upstream requires Firefox 2.x-era + Flash Player plugin ≥ 9), load a target SWF through the UI, and use the built-in Help menu for the manual.
