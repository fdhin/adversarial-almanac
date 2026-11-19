---
tool: "pytacle"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/124299/pytacle-alpha2.tar.gz"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/124299/pytacle-alpha2.tar.gz"
license: "GPL3"
blackarch-pkgver: "alpha2"
---

# Installing pytacle

> Automates the task of sniffing GSM frames

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pytacle) (version `alpha2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pytacle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/124299/pytacle-alpha2.tar.gz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libffi`
  - `pygtk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[pytacle]] · [[BlackArch Tools]]

