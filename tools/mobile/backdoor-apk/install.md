---
tool: "backdoor-apk"
build-system: "scripts"
upstream: "https://github.com/dana-at-cp/backdoor-apk"
recipe: "blackarch"
source: "https://github.com/dana-at-cp/backdoor-apk"
license: "Apache"
blackarch-pkgver: "141.2710126"
---

# Installing backdoor-apk

> Shell script that simplifies the process of adding a backdoor to any Android APK file

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backdoor-apk) (version `141.2710126`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S backdoor-apk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dana-at-cp/backdoor-apk) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `smali`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[backdoor-apk]] · [[BlackArch Tools]]

