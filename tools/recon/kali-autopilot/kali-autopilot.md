---
name: "kali-autopilot"
categories:
  - identify
version: "3.4-0kali3"
homepage: "https://gitlab.com/kalilinux/packages/kali-autopilot"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "threatcode/kali-autopilot"
github-stars: 1
github-pushed: "2023-04-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kali-autopilot

> Kali Purple's GUI workbench for building automated attack scripts — compose command sequences, deploy them to target machines over SSH, and run them as services for detection/response training.

## What it's for

Red/purple-team exercise scripting for Kali Purple's range of deliberately vulnerable training machines: instead of hand-writing attack automation, you define "challenges" (named attack scripts) as ordered command sequences in a local SQLite database, and kali-autopilot generates the runner — a bash script plus systemd service that executes the attack steps on the target — and can push and start it on machines over SSH (paramiko, optionally via a jump host). Its output is what purple-team defenders point their SIEM at: repeatable attack traffic for detection rule training. It is a wxPython GUI application with no CLI flags (the packaged `kali-autopilot` command just opens the window), and it orchestrates attacks you author — it ships no exploit content of its own. Upstream lives in Kali's GitLab; the GitHub `threatcode/kali-autopilot` repo is a mirror of the same tree.

## Typical use cases

### Open the attack-script workbench

```
kali-autopilot
```

Outcome: launches the GUI (desktop entry "Kali Autopilot — Automated Kali Attack Platform"), which manages its SQLite database under `~/kali-autopilot/`. From there you create a challenge, add its commands step by step, and export the generated `kali-autopilot_attack_<name>` service script; the same window handles SSH deployment to target machines. The generated per-challenge service script itself takes `-i` (install and start), `-r` (stop and remove) and `-d` (debug) on the target host.

*No corpus evidence — derived from the packaged source (Kali GitLab package repository; GUI entry point, no CLI surface).*
