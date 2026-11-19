---
tool: "webpwn3r"
build-system: "scripts"
upstream: "https://github.com/zigoo0/webpwn3r"
recipe: "blackarch"
source: "https://github.com/zigoo0/webpwn3r.git"
license: "GPL2"
blackarch-pkgver: "38.3d75e76"
---

# Installing webpwn3r

> A python based Web Applications Security Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webpwn3r) (version `38.3d75e76`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S webpwn3r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/zigoo0/webpwn3r.git
cd webpwn3r
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[webpwn3r]] · [[BlackArch Tools]]

