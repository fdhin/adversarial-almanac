---
name: "infection-monkey"
categories:
  - blackarch-networking
version: "v2.2.0.r3599.ga55fc8d"
homepage: "https://github.com/guardicore/monkey"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "guardicore/monkey"
github-stars: 7124
github-pushed: "2026-11-03"
github-archived: false
github-issues: 240
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# infection-monkey

> Open-source breach-and-attack simulation platform: a worm-like agent self-propagates through a data center (credential reuse, SSH/SMB/WMI exploits, Log4Shell, Zerologon) while the Monkey Island server maps the infection and reports what worked.

## What it's for

Infection Monkey (originally by Guardicore, now Akamai) tests how well a network withstands a perimeter breach and internal server infection. Two components cooperate: the Monkey agent, which infects machines and propagates using predefined and stolen credentials (Mimikatz) plus logical exploits such as SSH, SMB, WMI, Log4Shell and Zerologon; and Monkey Island, a dedicated server that configures runs, controls the agents, tracks propagation on a live map and produces a security report. Reach for it to validate segmentation, credential hygiene and patch levels against a realistic attacker workflow — it is not a vulnerability scanner, since it simulates attack paths rather than enumerating CVEs. The BlackArch package installs two launchers: `monkey-island` (the server) and `infection-monkey` (the agent).

## Typical use cases

### Start the Monkey Island command server

Bring up the server that configures, launches and visualizes attacks.

```
sudo monkey-island
```

Outcome: starts the Monkey Island web service locally; open its web UI in a browser to define propagation options, generate agent launch commands, watch the live infection map and read the post-run security report.

### Launch an agent against an island

Check the agent's run modes before enlisting it with the Island.

```
sudo infection-monkey --help
```

Outcome: prints the agent's positional run-mode choices — running in the current session versus detaching into a separate process (dropper mode); the exact mode token and registration details come from the Island UI's run dialog, which emits the full ready-to-paste command for the `infection-monkey` launcher.

*No corpus evidence — derived from upstream documentation.*
