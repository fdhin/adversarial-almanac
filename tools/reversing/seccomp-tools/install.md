---
tool: "seccomp-tools"
build-system: "scripts"
upstream: "https://github.com/david942j/seccomp-tools"
recipe: "blackarch"
aur-package: "seccomp-tools"
source: "https://github.com/david942j/seccomp-tools"
license: "MIT"
blackarch-pkgver: "1.6.1"
---

# Installing seccomp-tools

> Seccomp analysis toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/seccomp-tools) (version `1.6.1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S seccomp-tools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/david942j/seccomp-tools) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-os`
  - `ruby-racc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[seccomp-tools]] · [[BlackArch Tools]]

