---
name: "wmd"
categories:
  - blackarch-automation
version: "30.32e249a"
homepage: "https://github.com/ThomasTJdev/WMD"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "ThomasTJdev/WMD"
github-stars: 267
github-pushed: "2017-01-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wmd

> Metasploit-style console that wraps ~30 common security tools (brute-force, sniffing, phishing, WiFi, recon) as Python modules.

## What it's for

WMD ("Weapon of Mass Destruction") is a Python 3 console framework that unifies a toolbox of IT-security utilities behind a Metasploit-like interface: `use <module>` to load, `set <option> <value>` to configure, run. The modules are mostly wrappers — it shells out or imports aircrack-ng, Bettercap, John the Ripper, sqlmap, RouterSploit, SPARTA, changeme and others — organized in categories like brute-force (`bfssh`, `bfweb`, `bfzip`, `bfrar`), sniffing (`sniffhttp`, `apsniff`), phishing (`etphis` evil-twin, `webphis`), recon (`dig`, `dnsrecon`), WiFi (`wifiutils`, `createap`) and cracking (`john`, `crackwpa`, `hashid`). Before first run you must create `core/config.ini` from `core/config.ini.default` and set your environment (e.g. monitoring interface). Development ended in 2017, so treat it as a legacy launcher; the individual underlying tools have all moved on, and its value today is batch orchestration in labs rather than current attack capability.

## Typical use cases

### Load and configure a module in the console

```
python3 wmd.py
use bfssh
set host target.example.com
set user user
so
```

Outcome: starts the interactive console, loads the SSH brute-force module, sets its target options (`so` prints all module options with their current values), ready to run the attack from the module prompt.

### Fire a single module without the console

```
python3 wmd.py -m sniffhttp
```

Outcome: launches the HTTP sniffer module directly (any module CALL name works, e.g. `bfzip`, `apsniff`, `adminfinder`), skipping the interactive menu.

### Serve the web interface

```
python3 wmd.py -w
```

Outcome: starts a Flask server on `127.0.0.1:5000` listing all modules in the browser, where parameters can be filled in before a module launches in its own xterm.

### Register a newly written module

```
python3 wmd.py -a modules/my_module.py
```

Outcome: adds a custom module written from `modules/module_template.py` to the framework's module XML so it appears in `use` and the web menu.

*No corpus evidence — derived from upstream documentation.*
