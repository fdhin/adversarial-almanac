---
tool: "tlsenum"
build-system: "scripts"
upstream: "https://github.com/Ayrx/tlsenum"
recipe: "blackarch"
source: "https://github.com/Ayrx/tlsenum"
license: "MIT"
blackarch-pkgver: "78.787c88b"
---

# Installing tlsenum

> A command line tool to enumerate TLS cipher-suites supported by a server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tlsenum) (version `78.787c88b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tlsenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Ayrx/tlsenum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pip, python2-virtualenv (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[tlsenum]] · [[BlackArch Tools]]

