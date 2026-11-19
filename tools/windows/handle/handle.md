---
name: "handle"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://www.tarasco.org/security/handle/index.html"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-23"
build-system: windows
license: custom
meta-checked: "2026-11-09"
github-repo: "Seabreg/handle"
github-stars: 0
github-pushed: "2019-03-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# handle

> User-mode enumerator of open handles (files, tokens, ports, semaphores) across all Windows processes.

## What it's for

Handle is a small Windows utility by Andrés and Miguel Tarasco (tarasco.org, now Tarlogic) that walks the global object table and reports every handle held by every running process — files, tokens, semaphores, ports and other kernel objects. It works entirely in user mode, calling the undocumented NtQuerySystemInformation() and NtQueryObject() APIs, and its shipped source doubles as a worked example of how to enumerate system handles without a kernel driver. It predates convenient scriptable enumeration on Windows boxes where Sysinternals tools are not allowed. Do not confuse it with the better-known Sysinternals `handle.exe` (see [[sysinternals-suite]]) — the two are unrelated implementations of the same idea. The project is dead (the original site now serves Tarlogic's corporate pages and the download is gone) and it exposes no documented command-line interface, so this entry is description-only.

GitHub: third-party mirror of the dead tarasco.org distribution (original source and binary).

*No corpus evidence — derived from upstream documentation.*
