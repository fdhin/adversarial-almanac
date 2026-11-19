---
tool: "h2csmuggler"
build-system: "scripts"
upstream: "https://github.com/BishopFox/h2csmuggler"
recipe: "blackarch"
source: "https://github.com/BishopFox/h2csmuggler"
license: "MIT"
blackarch-pkgver: "7.7ea573a"
---

# Installing h2csmuggler

> HTTP Request Smuggling over HTTP/2 Cleartext (h2c).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/h2csmuggler) (version `7.7ea573a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S h2csmuggler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BishopFox/h2csmuggler) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `h2`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[h2csmuggler]] · [[BlackArch Tools]]

