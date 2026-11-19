---
tool: "windapsearch"
build-system: "scripts"
upstream: "https://github.com/ropnop/windapsearch"
recipe: "blackarch"
source: "https://github.com/ropnop/windapsearch"
license: "GPL3"
blackarch-pkgver: "28.7724ec4"
---

# Installing windapsearch

> Script to enumerate users, groups and computers from a Windows domain through LDAP queries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/windapsearch) (version `28.7724ec4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S windapsearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ropnop/windapsearch) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap`, `pyasn1`, `pyasn1-modules`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[windapsearch]] · [[BlackArch Tools]]

