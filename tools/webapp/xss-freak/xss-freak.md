---
name: "xss-freak"
categories:
  - blackarch-webapp
version: "17.e361766"
homepage: "https://github.com/hacker900123/XSS-Freak"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-04"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hacker900123/XSS-Freak"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# xss-freak

> XSS scanner written in Python 3; upstream repository deleted, so its CLI can no longer be verified.

## What it's for

XSS-Freak was an XSS scanner "fully written in Python3 from scratch" (upstream tagline), packaged by BlackArch from the last surviving commit of its now-deleted GitHub repository. The BlackArch recipe installs the single entry script `XSS-Freak.py` as `/usr/bin/xss-freak` and pulls in Python dependencies for HTTP requests (requests, beautifulsoup4), terminal coloring (colorama), and user-agent handling (python-user-agent), which indicates a crawler-style scanner over fetched pages. No README, source copy, or archived snapshot of the repository survives (the Wayback Machine only captured 404s), so the tool's command-line options cannot be verified against any documentation — treat it as historical. For maintained alternatives in the same category, see [[xspear]], [[xsscon]], or [[dalfox]].

No corpus evidence — no verifiable CLI survives; description-only note.
