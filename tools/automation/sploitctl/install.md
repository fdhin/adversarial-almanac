---
tool: "sploitctl"
build-system: "scripts"
upstream: "https://github.com/BlackArch/sploitctl"
recipe: "blackarch"
source: "https://github.com/BlackArch/sploitctl"
license: "GPL3"
blackarch-pkgver: "3.0.4"
---

# Installing sploitctl

> Fetch, install and search exploit archives from exploit sites like exploit-db and packetstorm.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sploitctl) (version `3.0.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sploitctl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/sploitctl) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pygit2`, `requests`, `termcolor`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`, `/usr/share/man`

Backlinks: [[automation]] · [[sploitctl]] · [[BlackArch Tools]]

