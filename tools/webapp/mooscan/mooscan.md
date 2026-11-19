---
name: "mooscan"
categories:
  - blackarch-webapp
version: "10.82963b0"
homepage: "https://github.com/vortexau/mooscan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "C0dak/mooscan"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 4
github-pushed: "2017-10-30"
github-archived: false
github-issues: 0
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mooscan

> Moodle LMS scanner for enumerating externally visible plugins, themes, and libraries.

## What it's for

MooScan (originally by vortexau; the live packaged source is the C0dak fork of the removed original repository) targets Moodle learning-management installations. Its goal is to enumerate installed plugins, themes, and libraries from the outside, serving administrators who want to know exactly what their Moodle install exposes, and penetration testers looking for vulnerable components. Be aware of its maturity: running the packaged version prints a banner and initializes its YAML configuration — the scanning engine is early-stage and incomplete, so treat it as a building block for Moodle enumeration rather than a finished scanner. The original upstream is gone (HTTP 404); development stopped years ago.

## Typical use cases

### Initialize the configuration for a scan

```
mooscan
```

Outcome: prints the banner, creates `~/.mooscan/` and copies the default `mooscan.conf` there, then prints the loaded YAML (keys include `git_path: moodle-git`, `module_database: modules.db`, `versions_database: versions.db`, `user_agent`, and update frequencies). Edit that file to point the Moodle git checkout and databases at your target before further use.

No corpus evidence — derived from the packaged source (C0dak/mooscan fork: `mooscan.py`, `lib/core/ConfigHandler.py`, `doc/mooscan.conf.skel`).
