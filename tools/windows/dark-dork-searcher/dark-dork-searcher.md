---
name: "dark-dork-searcher"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://rafale.org/~mattoufoutu/darkc0de.com/c0de/c/"
packaged-by: [blackarch]
blackarch-builddate: "2019-09-27"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dark-dork-searcher

> A darkc0de-era Windows GUI tool that mass-submits Google dork lists to collect matching URLs for follow-up testing.

*No corpus evidence — derived from inspection of the packaged binary; the original darkc0de.com distribution (mirrored at rafale.org) is no longer online.*

## What it's for

A 2009-era darkc0de community tool by Jonathan59, shipped as a standalone Visual Basic 6 GUI executable (`Dark-Dork Searcher.exe`, PE32 GUI subsystem). It automates the classic dork-scanning workflow of that period: the operator loads a list of web search dorks, the tool feeds them to a search engine and collects the matching URLs — output used to shortlist sites for manual SQL-injection, local-file-inclusion or admin-page hunting. It has no command-line interface and is entirely GUI-driven; everything happens in the window after launching the executable. Today it is mainly of historical interest: modern search engines block and re-CAPTCHA automated queries of this shape, the tool targets long-dead result-page layouts, and its homepage has been offline for years. Distributions package the original unmodified binary only (BlackArch installs it under `/usr/share/windows/dark-dork-searcher`), so there is nothing to configure or script.
