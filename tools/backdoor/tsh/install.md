---
tool: "tsh"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/search/?q=tsh"
recipe: "blackarch"
source: "http://packetstormsecurity.com/search/?q=tsh"
license: "custom"
blackarch-pkgver: "0.6"
---

# Installing tsh

> An open-source UNIX backdoor that compiles on all variants, has full pty support, and uses strong crypto for communication.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tsh) (version `0.6`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S tsh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/search/?q=tsh) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[tsh]] · [[BlackArch Tools]]

