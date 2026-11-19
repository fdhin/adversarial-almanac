---
tool: "tftp-proxy"
build-system: "scripts"
upstream: "http://www.c0decafe.de/"
recipe: "blackarch"
source: "http://www.c0decafe.de/"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing tftp-proxy

> This tool accepts connection on tftp and reloads requested content from an upstream tftp server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tftp-proxy) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tftp-proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.c0decafe.de/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[tftp-proxy]] · [[BlackArch Tools]]

