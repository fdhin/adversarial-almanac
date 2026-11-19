---
tool: "ripdc"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/scanner"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/scanner"
license: "custom:unknown"
blackarch-pkgver: "0.4"
---

# Installing ripdc

> A script which maps domains related to an given ip address or domainname.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ripdc) (version `0.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ripdc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/scanner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ripdc]] · [[BlackArch Tools]]

