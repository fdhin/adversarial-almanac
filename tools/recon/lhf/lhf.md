---
name: "lhf"
categories:
  - blackarch-recon
version: "40.51568ee"
homepage: "https://github.com/blindfuzzy/LHF"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "blindfuzzy/LHF"
github-stars: 275
github-pushed: "2018-04-21"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lhf

> "Low Hanging Fruit" — answer one prompt and LHF fans a single target out through nmap, arachni, dirb, hydra, sqlmap and enum4linux, collecting every output file in one results folder.

## What it's for

LHF (Low Hanging Fruit, by blindfuzzy) is a modular recon wrapper: instead of remembering and ordering the per-host toolchain yourself, it asks for one IP or URL and runs its module chain — nmap, arachni, dirb, hydra, sqlmap, enum4linux and others — writing every file each tool produces into a `results/` directory beside the install (with the BlackArch package, `/usr/share/lhf/results`). It is modular by design: a new tool drops into the Modules folder and gets wired into the run. Practical limits: it scans one target at a time, has no progress bar (output streams as tasks finish), and it is Python 2-era software untouched since 2018 — for current work, drive the underlying tools directly or use a maintained orchestrator.

## Typical use cases

### Point the whole module chain at one host

```
lhf
```

then answer the prompt:

```
198.51.100.10
```

Outcome: LHF checks permissions and dependencies first (decompressing the rockyou wordlist on first run), then prompts for the target IP or URL and runs every configured module against it, streaming status lines as tasks complete; afterwards all raw scan outputs (nmap, arachni and the rest) are collected under the `results/` folder in the LHF directory.

*No corpus evidence — derived from upstream documentation (blindfuzzy/LHF README).*
