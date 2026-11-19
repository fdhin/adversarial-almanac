---
tool: "sshprank"
build-system: "scripts"
upstream: "https://nullsecurity.net/tools/cracker.html"
recipe: "blackarch"
source: "https://nullsecurity.net/tools/cracker.html"
license: "custom:unknown"
blackarch-pkgver: "1.7.0"
---

# Installing sshprank

> A fast SSH mass-scanner, login cracker, banner grabber and password auth checker tool using the python-masscan and shodan module.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sshprank) (version `1.7.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sshprank    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://nullsecurity.net/tools/cracker.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `paramiko`, `python-masscan`, `shodan`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[sshprank]] · [[BlackArch Tools]]

