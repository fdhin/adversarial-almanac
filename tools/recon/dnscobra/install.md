---
tool: "dnscobra"
build-system: "scripts"
upstream: "https://github.com/dmitescu/dnscobra"
recipe: "blackarch"
source: "https://github.com/dmitescu/dnscobra"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing dnscobra

> DNS subdomain bruteforcing tool with Tor support through torsocks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnscobra) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dnscobra    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dmitescu/dnscobra) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dnscobra]] · [[BlackArch Tools]]

