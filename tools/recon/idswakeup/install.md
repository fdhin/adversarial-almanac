---
tool: "idswakeup"
build-system: "scripts"
upstream: "http://www.hsc.fr/ressources/outils/idswakeup/index.html.en"
recipe: "blackarch"
source: "http://www.hsc.fr/ressources/outils/idswakeup/index.html.en"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing idswakeup

> A collection of tools that allows to test network intrusion detection systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/idswakeup) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S idswakeup    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.hsc.fr/ressources/outils/idswakeup/index.html.en) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[idswakeup]] · [[BlackArch Tools]]

