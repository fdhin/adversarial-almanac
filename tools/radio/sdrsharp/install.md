---
tool: "sdrsharp"
build-system: "other"
upstream: "https://airspy.com"
recipe: "blackarch"
aur-package: "sdrsharp"
source: "https://airspy.com"
license: "LicenseRef-SDRSharp"
blackarch-pkgver: "1.0.0.1457"
---

# Installing sdrsharp

> The most popular SDR program.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sdrsharp) (version `1.0.0.1457`, license LicenseRef-SDRSharp).

## Arch / BlackArch

```bash
sudo pacman -S sdrsharp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://airspy.com) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `mono`
- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `bash`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `icoutils`
  - `portaudio` — Debian libportaudio2 / Fedora portaudio-devel
  - `rtl-sdr`
  - `unzip`

## Exact build steps from the recipe

```bash
mkdir build || true

  mono --aot --optimize=all ${_sdrsharp}/*.exe
  mv ${_sdrsharp}/*.exe.so build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/icons`

Backlinks: [[radio]] · [[sdrsharp]] · [[BlackArch Tools]]

