---
name: "riwifshell"
categories:
  - blackarch-webapp
version: "38.40075d5"
homepage: "https://github.com/graniet/riwifshell"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "graniet/riwifshell"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# riwifshell

> PHP web backdoor with a built-in file explorer, server infector and kernel-exploit helper.

## What it's for

riwifshell ("Riwif Web Backdoor", by graniet) is a 2016-era web backdoor: you
upload the PHP payload to a compromised web server and then operate the
infected host through an in-browser command interface. Beyond plain command
execution it ships an interactive file explorer for browsing, reading and
managing files on the server, an "infect" mode, an inspector component it can
download onto the server, and a kernel listing that suggests kernel exploits
applicable to the host. The original GitHub repository is gone; the packaged
python2-era source and public mirrors are the only references, and its
undocumented client entry point leaves no verifiable CLI invocation — treat
it as a historical artifact and prefer maintained alternatives.

*No corpus evidence — description derived from upstream documentation (graniet/riwifshell README, verified via the Exploit-install/riwifshell mirror); description-only note: no verifiable CLI entry point survives the deleted upstream.*
