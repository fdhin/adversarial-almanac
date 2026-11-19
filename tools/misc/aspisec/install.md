---
tool: "aspisec"
build-system: "scripts"
upstream: "https://noraj.github.io/aspisec/"
recipe: "blackarch"
aur-package: "aspisec"
source: "https://noraj.github.io/aspisec/"
license: "MIT"
blackarch-pkgver: "0.3.0"
---

# Installing aspisec

> Removes the traces left by offensive security tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aspisec) (version `0.3.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S aspisec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://noraj.github.io/aspisec/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[aspisec]] · [[BlackArch Tools]]

