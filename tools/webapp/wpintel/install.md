---
tool: "wpintel"
build-system: "scripts"
upstream: "https://github.com/Tuhinshubhra/WPintel"
recipe: "blackarch"
source: "https://github.com/Tuhinshubhra/WPintel"
license: "custom:unknown"
blackarch-pkgver: "6.741c0c9"
---

# Installing wpintel

> Chrome extension designed for WordPress Vulnerability Scanning and information gathering.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpintel) (version `6.741c0c9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wpintel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Tuhinshubhra/WPintel) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `chromium`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wpintel]] · [[BlackArch Tools]]

