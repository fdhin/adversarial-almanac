---
name: "autonse"
categories:
  - blackarch-automation
version: "25.7c87f4c"
homepage: "https://github.com/m4ll0k/AutoNSE"
packaged-by: [blackarch]
blackarch-builddate: "2021-01-16"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "m4ll0k/AutoNSE"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# autonse

> Interactive Bash wrapper that sweeps Nmap Scripting Engine (NSE) scripts across targets and collects the output in one place.

## What it's for

AutoNSE is a menu-driven Bash script that automates running Nmap's NSE scripts: instead of assembling `nmap --script` invocations by hand, you answer prompts for the target specification and an output path, and it runs an NSE sweep against the targets with nmap and stores the results on disk. It has no command-line flags — everything is interactive — and it depends on `nmap` and `iputils` being installed. It only automates NSE execution; it does not pick exploits or interpret results for you. The upstream repository is gone (the README was recovered from the Wayback archive), so the packaged script is the remaining point of reference.

*No corpus evidence — derived from the archived upstream README (repo gone; read via the Wayback archive).*

## Typical use cases

### Run an NSE sweep interactively

Point the whole NSE catalog at one or more targets without writing the nmap command yourself.

```
autonse
```

Outcome: the script asks for the nmap target specification and the output path (in the packaged version the wrapper runs `autonse.sh`; from a source checkout the equivalent entry point is `bash autonse.sh`), then executes the NSE scan run and writes each result under the output directory you supplied — review the per-target output files for the NSE findings.
