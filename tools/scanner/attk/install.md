---
tool: "attk"
build-system: "scripts"
upstream: "https://spnsupport.trendmicro.com/"
recipe: "blackarch"
source: "https://spnsupport.trendmicro.com/"
license: "custom:unknown"
blackarch-pkgver: "2.0.1023"
---

# Installing attk

> Trend Micro Anti-Threat Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/attk) (version `2.0.1023`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S attk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://spnsupport.trendmicro.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[attk]] · [[BlackArch Tools]]

