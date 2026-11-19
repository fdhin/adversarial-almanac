---
tool: "sharesniffer"
build-system: "scripts"
upstream: "https://github.com/shirosaidev/sharesniffer"
recipe: "blackarch"
source: "https://github.com/shirosaidev/sharesniffer"
license: "Apache-2.0"
blackarch-pkgver: "v0.1.b.8.r8.ga0c5ed6"
---

# Installing sharesniffer

> Network share sniffer and auto-mounter for crawling remote file systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sharesniffer) (version `v0.1.b.8.r8.ga0c5ed6`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S sharesniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/shirosaidev/sharesniffer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `netifaces`, `python-nmap`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sharesniffer]] · [[BlackArch Tools]]

