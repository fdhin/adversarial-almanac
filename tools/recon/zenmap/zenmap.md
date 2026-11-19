---
name: "zenmap"
categories:
  - information-gathering
version: "7.993-1"
homepage: "https://nmap.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-28"
build-system: python
meta-checked: "2026-11-09"
github-repo: "nmap/nmap"
github-stars: 13959
github-pushed: "2026-11-09"
github-archived: false
github-issues: 726
github-match: "search"
gh-checked: "2026-11-09"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 541
gh-activity-checked: "2026-11-09"
---

# zenmap

> The official Nmap GUI: interactive scan profiles, searchable results, scan diffing and a network topology viewer.

## What it's for

Zenmap is the Nmap Project's cross-platform front end for nmap. Instead of memorizing flag combinations you pick a command profile (Intense scan, Quick scan, Quick scan plus, ...) from a dropdown, edit it visually, and launch; results land in a browsable, filterable table that you can save and later reopen or diff against another scan of the same hosts to see what changed. Its topology view draws the discovered network as an interactive radial map, and a profile editor lets you save your own frequently-used command lines. Zenmap does not scan by itself — it builds and runs nmap commands, so everything nmap supports is available and everything it does not is unavailable; for headless/automation work plain nmap output (or nmap's XML + other front ends) is the better fit.

## Typical use cases

### Launch a scan with a preset profile and target

Start scanning without composing flags by hand.

```
zenmap -p "Quick scan" -t target.example.com
```

Outcome: opens the GUI with the "Quick scan" profile selected and the target pre-filled; press Scan to run the equivalent `nmap -T4 -F` command. Any profile name shown in the GUI's profile dropdown works, and a target given as a positional argument (`zenmap target.example.com`) does the same with the default profile.

### Reopen a saved scan for browsing or diffing

Review earlier results without re-scanning.

```
zenmap -f saved-scan.xml
```

Outcome: loads the saved nmap/XML results file into the GUI's ports, hosts and services views; open a second saved scan and use Tools → Compare Scans to show hosts/ports that appeared, disappeared or changed state between the two runs.

*No corpus evidence — derived from upstream documentation.*
