---
name: "powershell-empire"
categories:
  - backdoor
version: "6.6.0-0parrot7"
homepage: "https://github.com/BC-SECURITY/Empire"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, aur]
build-system: python
meta-checked: "2026-11-09"
github-repo: "BC-SECURITY/Empire"
github-stars: 5348
github-pushed: "2026-09-08"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 354
gh-activity-checked: "2026-11-09"
---

# powershell-empire

> Parrot's `powershell-empire` package — the BC-Security Empire post-exploitation/C2 framework; full usage is documented on [[empire]].

## What it's for

This note covers the Parrot package `powershell-empire`, which ships the same BC-SECURITY/Empire codebase already documented end-to-end under [[empire]] (the BlackArch packaging of the identical upstream). Empire is a post-exploitation and adversary-emulation framework that historically became famous for its pure PowerShell 2.0 Windows agent and pure Python Linux/OS X agent; the current 6.x line is a full C2 with agents for PowerShell, Python 3, C#, IronPython and Go, operated through the Starkiller web GUI or the REST API. Listener, stager, agent and API workflows — including first-start credentials — live on [[empire]] and are deliberately not repeated here. One compatibility note: Empire 6.0 removed the old interactive terminal console (`listeners` / `usestager` / `agents` / `interact`), so the classic menu-driven workflow only exists in 4.x-era releases and old guides.

*No corpus evidence — derived from upstream documentation.*
