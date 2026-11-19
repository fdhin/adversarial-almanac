---
tool: "chronoleak"
build-system: "scripts"
upstream: "https://github.com/noraj/ChronoLeak"
recipe: "blackarch"
aur-package: "chronoleak"
source: "https://github.com/noraj/ChronoLeak"
license: "MIT"
blackarch-pkgver: "0.0.1"
---

# Installing chronoleak

> ICMP Timestamp Remote Time Leaker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chronoleak) (version `0.0.1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S chronoleak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/noraj/ChronoLeak) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `hping`
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[chronoleak]] · [[BlackArch Tools]]

