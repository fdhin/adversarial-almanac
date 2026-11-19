---
tool: "zipdump"
build-system: "scripts"
upstream: "https://blog.didierstevens.com/my-software/#zipdump"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#zipdump"
license: "custom:unknown"
blackarch-pkgver: "0.0.21"
---

# Installing zipdump

> ZIP dump utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zipdump) (version `0.0.21`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S zipdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blog.didierstevens.com/my-software/#zipdump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[zipdump]] · [[BlackArch Tools]]

