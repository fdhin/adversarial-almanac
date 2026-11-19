---
tool: "rej"
build-system: "scripts"
upstream: "https://sourceforge.net/projects/rejava/"
recipe: "blackarch"
aur-package: "rej"
source: "https://downloads.sourceforge.net/project/rejava/reJ/reJ%20v0.7/rej_v0.7_bin.zip"
license: "GPL2"
blackarch-pkgver: "0.7"
---

# Installing rej

> An API and a graphical tool for inspection and manipulation of classfiles for the Java platform.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rej) (version `0.7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S rej    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://downloads.sourceforge.net/project/rejava/reJ/reJ%20v0.7/rej_v0.7_bin.zip
tar xf rej_v0.7_bin.zip
cd rej_v0.7_bin
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[rej]] · [[BlackArch Tools]]

