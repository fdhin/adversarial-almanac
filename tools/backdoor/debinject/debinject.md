---
name: "debinject"
categories:
  - blackarch-backdoor
version: "43.d884309"
homepage: "https://github.com/UndeadSec/Debinject"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD3
meta-checked: "2026-11-09"
github-repo: "UndeadSec/Debinject"
github-stars: 295
github-pushed: "2022-04-26"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# debinject

> Interactive wizard that trojans Debian packages by injecting a Metasploit payload and repacking them.

## What it's for

Debinject takes a legitimate `.deb` package and a Metasploit payload, injects the payload into the package's payload/scripts so it executes during installation, and repacks the result as an installable trojaned `.deb` — classic supply-chain simulation for Debian-based targets. It is a menu-driven Python script over `dpkg`/`dpkg-deb` and `msfvenom`/metasploit; there are no non-interactive CLI flags. The project is archived upstream and written for Python 2, so on current distributions expect to provide a Python 2 interpreter yourself (see the install notes); its behaviour, not its maintenance state, is what the note documents.

## Typical use cases

### Run the injection wizard

Trojan a package interactively.

```
sudo python debinject.py
```

Outcome: the wizard prompts for the path to the target `.deb` and for the Metasploit payload details (payload type, callback host and port), then unpacks the package, injects and rebuilds it; the resulting trojaned `.deb` executes the chosen payload when installed on a victim with `dpkg -i`/`apt`, opening the configured reverse connection to your handler.

*No corpus evidence — derived from upstream documentation.*
