---
name: "massexpconsole"
categories:
  - blackarch-automation
version: "v2.3.5.r1.g530c880"
homepage: "https://github.com/jm33-m0/massExpConsole"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "jm33-m0/massExpConsole"
github-stars: 615
github-pushed: "2022-07-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# massexpconsole

> Prompt console (mec) for firing one exploit module at many hosts.

## What it's for

MassExploitConsole (`mec`, jm33-m0) wraps a small fixed set of adapted exploit modules — an SSH brute-forcer plus exploits such as weblogic_cve201710271 and webmin_cve_2019_15107 — behind a prompt-toolkit console with TAB completion and history, and adds target discovery through ZoomEye and Censys searches and a simple Baidu crawler. IP-hiding is built in via proxychains4 and a proxy pool. It is meant for authorized mass exploitation of ranges you control; it is a thin automation layer, not a framework — the author notes it is a first Python project with rough edges. Meant only for authorized systems per its disclaimer.

## Typical use cases

### Start the console

Open the mec prompt.

```
mec
```

Outcome: launches the interactive console; commands auto-complete as you type and TAB triggers completion explicitly; arrow keys and mouse reposition the cursor.

### Launch a mass-exploit job through the wizard

Drive one full run from the prompt.

```
start
```

Outcome: walks through the prompts — proxychains yes/no, built-in vs. your own exploit, module choice (`ssh_bruteforcer`, `weblogic_cve201710271`, `webmin_cve_2019_15107`, `witbe`), and the password list under `./data` — then executes the module against your targets with process-level concurrency.

*No corpus evidence — derived from upstream documentation.*
