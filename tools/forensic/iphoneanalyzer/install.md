---
tool: "iphoneanalyzer"
build-system: "scripts"
upstream: "http://downloads.sourceforge.net/project/iphoneanalyzer/"
recipe: "blackarch"
source: "http://downloads.sourceforge.net/project/iphoneanalyzer/"
license: "GPL3"
blackarch-pkgver: "2.1.0"
---

# Installing iphoneanalyzer

> Allows you to forensically examine or recover date from in iOS device.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iphoneanalyzer) (version `2.1.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S iphoneanalyzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://downloads.sourceforge.net/project/iphoneanalyzer/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[iphoneanalyzer]] · [[BlackArch Tools]]

