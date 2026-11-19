---
tool: "jcrack"
build-system: "scripts"
upstream: "http://www.thedrahos.net/jcrack/"
recipe: "blackarch"
source: "http://www.thedrahos.net/jcrack/jcrack.sh"
license: "custom:unknown"
blackarch-pkgver: "0.3.6"
---

# Installing jcrack

> A utility to create dictionary files that will crack the default passwords of select wireless gateways

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jcrack) (version `0.3.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.thedrahos.net/jcrack/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `crunch`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[jcrack]] · [[BlackArch Tools]]

