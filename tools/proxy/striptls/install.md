---
tool: "striptls"
build-system: "scripts"
upstream: "https://github.com/tintinweb/striptls"
recipe: "blackarch"
source: "https://github.com/tintinweb/striptls"
license: "custom:unknown"
blackarch-pkgver: "55.5ec712c"
---

# Installing striptls

> Proxy PoC implementation of STARTTLS stripping attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/striptls) (version `55.5ec712c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S striptls    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tintinweb/striptls) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[striptls]] · [[BlackArch Tools]]

