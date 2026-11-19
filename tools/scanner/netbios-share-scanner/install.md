---
tool: "netbios-share-scanner"
build-system: "scripts"
upstream: "http://www.secpoint.com/netbios-share-scanner.html"
recipe: "blackarch"
source: "http://www.secpoint.com/netbios-share-scanner.html"
license: "BSD"
blackarch-pkgver: "1.0"
---

# Installing netbios-share-scanner

> This tool could be used to check windows workstations and servers if they have accessible shared resources.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netbios-share-scanner) (version `1.0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S netbios-share-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.secpoint.com/netbios-share-scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[netbios-share-scanner]] · [[BlackArch Tools]]

