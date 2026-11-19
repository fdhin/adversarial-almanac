---
tool: "country-ip-blocks"
build-system: "scripts"
upstream: "https://github.com/herrbischoff/country-ip-blocks"
recipe: "blackarch"
source: "https://github.com/herrbischoff/country-ip-blocks"
license: "CC0-1.0"
blackarch-pkgver: "6364.2081e7b"
---

# Installing country-ip-blocks

> CIDR country-level IP data, straight from the Regional Internet Registries, updated hourly.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/country-ip-blocks) (version `6364.2081e7b`, license CC0-1.0).

## Arch / BlackArch

```bash
sudo pacman -S country-ip-blocks    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/herrbischoff/country-ip-blocks) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wordlist]] · [[country-ip-blocks]] · [[BlackArch Tools]]

