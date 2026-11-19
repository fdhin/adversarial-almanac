---
tool: "fi6s"
build-system: "scripts"
upstream: "https://github.com/sfan5/fi6s"
recipe: "blackarch"
source: "https://github.com/sfan5/fi6s"
license: "GAGPL"
blackarch-pkgver: "258.765fcf3"
---

# Installing fi6s

> IPv6 network scanner designed to be fast.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fi6s) (version `258.765fcf3`, license GAGPL).

## Arch / BlackArch

```bash
sudo pacman -S fi6s    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sfan5/fi6s) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[fi6s]] · [[BlackArch Tools]]

