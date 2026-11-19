---
name: "sparta"
categories:
  - blackarch-scanner
version: "21.b0a4514"
homepage: "http://sparta.secforce.com/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-06-18"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SECFORCE/sparta"
github-stars: 1667
github-pushed: "2021-06-02"
github-archived: false
github-issues: 22
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sparta

> PyQt GUI for the scanning and enumeration phase: add hosts, let nmap run, and click through per-service tool output from one window.

## What it's for

SPARTA (SECFORCE) is a Python GUI application that simplifies network infrastructure penetration testing. The tester adds hosts (or subnets) to the hosts panel; SPARTA runs nmap against them and displays the discovered hosts and services in tables, lets you launch configured tools against any service from the context menu, and collects all tool output in one place — with a brute-force tab driving hydra and a notes/credentials workflow for findings. Every tool command is customizable per service type, since each tester has their own methods and preferences. It requires nmap (for adding hosts) and hydra (for the brute tab) and is otherwise a thin, configurable orchestration layer — it does not include scanners of its own. Purely interactive (no scripting surface beyond launching it), and the project has been quiet since its v2.0 era.

## Typical use cases

### Launch the GUI and start an assessment

```
sparta
```

Outcome: opens the SPARTA window. Add a host or subnet via the hosts panel; SPARTA runs its default nmap scan, populates the hosts/services tables, and from then on clicking a service shows its tool output while the context menu offers the enumeration and attack commands configured for that service type. Adjust which tools run per service (and their flags) in the tool configuration dialog rather than editing scripts.

*No corpus evidence — derived from upstream documentation.*
