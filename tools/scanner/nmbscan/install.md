---
tool: "nmbscan"
build-system: "scripts"
upstream: "http://nmbscan.gbarbier.org/"
recipe: "blackarch"
source: "http://nmbscan.gbarbier.org/"
license: "GPL"
blackarch-pkgver: "1.2.6"
---

# Installing nmbscan

> Tool to scan the shares of a SMB/NetBIOS network, using the NMB/SMB/NetBIOS protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nmbscan) (version `1.2.6`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S nmbscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nmbscan.gbarbier.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `samba`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[nmbscan]] · [[BlackArch Tools]]

