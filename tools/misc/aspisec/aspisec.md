---
name: "aspisec"
categories:
  - blackarch-misc
version: "0.3.0"
homepage: "https://noraj.github.io/aspisec/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-08-09"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Acceis/aspisec"
github-stars: 15
github-pushed: "2024-10-24"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aspisec

> Removes the traces left by offensive security tools on the auditor's own machine.

*No corpus evidence — derived from upstream documentation.*

## What it's for

AspiSec (Ruby gem, by Acceis) vacuums up the artifacts offensive tooling leaves behind on the pentester's workstation after an engagement: tool histories and logs, saved loot, pot files, screenshot folders, cached credentials and shell/interpreter histories. Around 25 tool modules cover the usual suspects (metasploit, hashcat, john, sqlmap, ffuf, bloodhound, netexec, amass, …), each split into named locations you can enable or disable in a config file. It is post-engagement hygiene on systems you own — not an anti-forensic wiper aimed at third-party hosts. CLI and Ruby library in one package.

## Typical use cases

### Review what would be cleaned

Inventory the supported tools and the locations AspiSec knows about.

```
aspisec list
```

Outcome: prints one line per tool module (metasploit, hashcat, sqlmap, …) with its covered locations (logs, history, potfile, screenshots, workspaces) and status markers, so you can see exactly which artifacts on this machine are in scope before deleting anything.

### Clean the leftovers

Run the removal pass.

```
aspisec clean
```

Outcome: walks the enabled modules and removes their caches, histories, logs and loot; by default it is interactive, showing each file/directory with its description before acting, so a run ends with the workstation stripped of engagement residue. Behavior (interactive vs silent, per-location toggles) is set in the config file.

### Run a verbose pass for auditing

Raise the log level to see each decision.

```
aspisec clean --debug
```

Outcome: displays the parsed arguments and additional detail about what the run does — useful when writing or validating a config where you need to confirm precisely which paths a `clean` will touch.
