---
tool: "eazy"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/117572/EAZY-Web-Scanner.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/EAZY.zip"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing eazy

> This is a small python tool that scans websites to look for PHP shells, backups, admin panels, and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eazy) (version `0.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S eazy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/scanners/EAZY.zip
tar xf EAZY.zip
cd EAZY
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[eazy]] · [[BlackArch Tools]]

