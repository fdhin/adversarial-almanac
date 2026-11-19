---
name: "idb"
categories:
  - blackarch-mobile
version: "2.10.3"
homepage: "https://github.com/dmayer/idb"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "dmayer/idb"
github-stars: 955
github-pushed: "2023-03-25"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# idb

> Qt GUI workbench that bundles the routine tasks of iOS app pentesting.

## What it's for

idb, by Daniel A. Mayer, is a Qt-based GUI for the chores of iOS app
pentesting against a jailbroken device or the simulator: connecting over SSH
or usbmuxd, listing and extracting installed apps (including CgBI/fairplay
binary handling and class-dump/otool-driven analysis), keychain inspection,
screenshot capture, certificate-authority management, and URL-scheme fuzzing.
It is GUI-first by design — the installed `idb` command simply opens the
application window, and the original command-line version was discontinued
upstream — so there is no scriptable CLI to document here. Development is
dormant (Ruby/Qt-era code); for current iOS versions expect the helper tools it
drives to need newer replacements.

*No corpus evidence — derived from upstream documentation.*
