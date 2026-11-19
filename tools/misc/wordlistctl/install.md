---
tool: "wordlistctl"
build-system: "scripts"
upstream: "https://github.com/BlackArch/wordlistctl"
recipe: "blackarch"
source: "https://github.com/BlackArch/wordlistctl"
license: "GPL3"
blackarch-pkgver: "0.9.4"
---

# Installing wordlistctl

> Fetch, install and search wordlist archives from websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wordlistctl) (version `0.9.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wordlistctl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/wordlistctl) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `termcolor`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`, `/usr/share/man`

Backlinks: [[misc]] · [[wordlistctl]] · [[BlackArch Tools]]

