---
tool: "facebash"
build-system: "scripts"
upstream: "https://github.com/thelinuxchoice/facebash"
recipe: "blackarch"
source: "https://github.com/thelinuxchoice/facebash"
license: "GPL3"
blackarch-pkgver: "17.95c3c25"
---

# Installing facebash

> Facebook Brute Forcer in shellscript using TOR.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/facebash) (version `17.95c3c25`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S facebash    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thelinuxchoice/facebash) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `curl`
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[facebash]] · [[BlackArch Tools]]

