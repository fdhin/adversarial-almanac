---
tool: "quickscope"
build-system: "scripts"
upstream: "https://github.com/CYB3RMX/Qu1cksc0pe"
recipe: "blackarch"
source: "https://github.com/CYB3RMX/Qu1cksc0pe"
license: "GPL-3.0-or-later"
blackarch-pkgver: "555.180d44f"
---

# Installing quickscope

> Statically analyze windows, linux, osx, executables and also APK files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/quickscope) (version `555.180d44f`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S quickscope    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CYB3RMX/Qu1cksc0pe) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[quickscope]] · [[BlackArch Tools]]

