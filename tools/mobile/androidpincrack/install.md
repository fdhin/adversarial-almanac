---
tool: "androidpincrack"
build-system: "scripts"
upstream: "https://github.com/PentesterES/AndroidPINCrack"
recipe: "blackarch"
source: "https://github.com/PentesterES/AndroidPINCrack"
license: "MIT"
blackarch-pkgver: "5.6439bd2"
---

# Installing androidpincrack

> Bruteforce the Android Passcode given the hash and salt.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androidpincrack) (version `5.6439bd2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S androidpincrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/PentesterES/AndroidPINCrack) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[androidpincrack]] · [[BlackArch Tools]]

