---
tool: "android-ndk"
build-system: "scripts"
upstream: "https://developer.android.com/ndk/downloads"
recipe: "blackarch"
aur-package: "android-ndk"
source: "https://developer.android.com/ndk/downloads"
license: "GPL, LGPL, custom"
blackarch-pkgver: "r29"
---

# Installing android-ndk

> Android C/C++ developer kit

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/android-ndk) (version `r29`, license GPL, LGPL, custom).

## Arch / BlackArch

```bash
sudo pacman -S android-ndk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://developer.android.com/ndk/downloads) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `android-sdk`
  - `glibc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[android-ndk]] · [[BlackArch Tools]]

