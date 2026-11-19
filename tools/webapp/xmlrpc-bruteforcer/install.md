---
tool: "xmlrpc-bruteforcer"
build-system: "scripts"
upstream: "https://github.com/kavishgr/xmlrpc-bruteforcer"
recipe: "blackarch"
source: "https://github.com/kavishgr/xmlrpc-bruteforcer"
license: "custom:unknown"
blackarch-pkgver: "36.6a46371"
---

# Installing xmlrpc-bruteforcer

> An XMLRPC brute forcer targeting Wordpress written in Python 3.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xmlrpc-bruteforcer) (version `36.6a46371`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xmlrpc-bruteforcer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/kavishgr/xmlrpc-bruteforcer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xmlrpc-bruteforcer]] · [[BlackArch Tools]]

