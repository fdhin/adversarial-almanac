---
tool: "shitflood"
build-system: "scripts"
upstream: "https://github.com/acidvegas/shitflood"
recipe: "blackarch"
source: "https://github.com/acidvegas/shitflood"
license: "custom:unknown"
blackarch-pkgver: "14.e74fc42"
---

# Installing shitflood

> A Socks5 clone flooder for the Internet Relay Chat (IRC) protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shitflood) (version `14.e74fc42`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S shitflood    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/acidvegas/shitflood) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[shitflood]] · [[BlackArch Tools]]

