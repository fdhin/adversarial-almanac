---
tool: "iptodomain"
build-system: "scripts"
upstream: "https://github.com/Hackplayers/iptodomain"
recipe: "blackarch"
source: "https://github.com/Hackplayers/iptodomain"
license: "GPL3"
blackarch-pkgver: "18.f1afcd7"
---

# Installing iptodomain

> This tool extract domains from IP address based in the information saved in virustotal.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iptodomain) (version `18.f1afcd7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S iptodomain    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Hackplayers/iptodomain) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[iptodomain]] · [[BlackArch Tools]]

