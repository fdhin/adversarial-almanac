---
name: "pyinstaller-hooks-contrib"
categories:
  - blackarch-misc
version: "2026.7"
homepage: "https://github.com/pyinstaller/pyinstaller-hooks-contrib"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-11-02"
build-system: python
license: custom:mixed
meta-checked: "2026-11-09"
github-repo: "pyinstaller/pyinstaller-hooks-contrib"
github-stars: 125
github-pushed: "2026-11-02"
github-archived: false
github-issues: 43
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 136
gh-activity-checked: "2026-11-09"
---

# pyinstaller-hooks-contrib

> Community-maintained hooks that teach PyInstaller how to package third-party Python libraries.

## What it's for

PyInstaller discovers imports by static analysis, which misses runtime data files, dynamically loaded modules, and package-specific quirks. A "hook" extends PyInstaller for a single package: package hooks tell it which data files and hidden imports to collect, runtime hooks set up the environment before the bundled app starts. This repository is the upstream PyInstaller organization's shared collection of such hooks for many popular packages, and it is pulled in automatically as a dependency of PyInstaller itself, which is why bundling numpy, matplotlib, PyQt, and similar packages works out of the box. It is a support library consumed by `pyinstaller` at build time — it ships no command-line interface of its own, so there is nothing to invoke directly. When a package bundles incorrectly, updating this package (or contributing a hook for the offending package upstream) is the fix.

*No corpus evidence — derived from upstream documentation.*
