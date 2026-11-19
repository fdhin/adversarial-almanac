---
tool: "android-sdk"
build-system: "scripts"
upstream: "https://developer.android.com/studio/releases/sdk-tools.html"
recipe: "blackarch"
aur-package: "android-sdk"
source: "https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip"
license: "custom"
blackarch-pkgver: "26.1.1"
---

# Installing android-sdk

> Google Android SDK

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/android-sdk) (version `26.1.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S android-sdk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
tar xf sdk-tools-linux-4333796.zip
cd sdk-tools-linux-4333796
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `fontconfig`
  - `freetype2`
  - `lib32-gcc-libs`
  - `lib32-glibc`
  - `libx11`
  - `libxext`
  - `libxrender`
  - `libxtst`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[android-sdk]] · [[BlackArch Tools]]

