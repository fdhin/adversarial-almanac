---
tool: "evil-ssdp"
build-system: "scripts"
upstream: "https://gitlab.com/initstring/evil-ssdp"
recipe: "blackarch"
source: "https://gitlab.com/initstring/evil-ssdp"
license: "MIT"
blackarch-pkgver: "08..beta.r7.g0d41a11"
---

# Installing evil-ssdp

> Spoof SSDP replies to phish for NetNTLM challenge/response on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evil-ssdp) (version `08..beta.r7.g0d41a11`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S evil-ssdp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://gitlab.com/initstring/evil-ssdp) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `netifaces`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[evil-ssdp]] · [[BlackArch Tools]]

