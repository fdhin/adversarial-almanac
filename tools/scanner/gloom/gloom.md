---
name: "gloom"
categories:
  - blackarch-scanner
version: "93.cd6e927"
homepage: "https://github.com/StreetSec/Gloom-Framework"
packaged-by: [blackarch]
blackarch-builddate: "2021-02-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "StreetSec/Gloom-Framework"
github-stars: 56
github-pushed: "2017-10-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gloom

> Menu-driven Python 2 penetration-testing console bundling recon, LAN discovery, wireless jamming and fuzzing mini-tools.

## What it's for

Gloom Framework (Josh, packaged from StreetSec/Gloom-Framework) is an all-in-one interactive
console for Linux: a single `gloom` prompt from which you launch bundled modules — admin-panel
finding, WHOIS, IP geolocation, LAN device discovery, website IP resolution, WAF detection, port
scanning, payload generation, Wi-Fi deauthentication, anonymous email, email-breach checking, and
TCP/FTP/HTTP fuzzers. It must run as root and is strictly interactive: there is no per-module
CLI, no scripting and no headless mode — the packaged entry point just opens the console. The
framework depends on Python 2 and has been unmaintained since 2017, so treat it as legacy;
individual mature tools cover every module it ships.

## Typical use cases

### Open the console and list what it can do

Start the framework and enumerate its commands.

```
sudo gloom
```

Outcome: verifies it is running as root on Linux and drops you at the red `[gloom]` prompt. Type
`help` to print the core commands (`help`, `info`, `clear`, `exit`) plus the full module list, or
`info` for the framework's version and author details.

### Run a module from the prompt

Invoke one of the bundled tools interactively.

```
web_whois
```

Outcome: at the `[gloom]` prompt, typing a module name such as `web_whois`, `port_scanner`,
`waf_scanning` or `tcp_fuzzer` starts that tool and prompts for its inputs (for example
`Website>` for `web_whois`, which then prints the registrant contact record). When the module
finishes you are returned to the prompt.

*No corpus evidence — derived from upstream source (gloom.py console loop and `help` output read
from the packaged tarball; BlackArch wraps it in a python2 launcher).*
