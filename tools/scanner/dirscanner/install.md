---
tool: "dirscanner"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/117773/Directory-Scanner-Tool.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/Scanner.tar.gz"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing dirscanner

> This is a python script that scans webservers looking for administrative directories, php shells, and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirscanner) (version `0.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S dirscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/scanners/Scanner.tar.gz
tar xf Scanner.tar.gz
cd Scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dirscanner]] · [[BlackArch Tools]]

