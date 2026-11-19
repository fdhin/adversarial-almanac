---
tool: "graudit"
build-system: "autotools"
upstream: "https://github.com/wireghoul/graudit"
recipe: "blackarch"
aur-package: "graudit"
source: "https://github.com/wireghoul/graudit"
license: "GPL"
blackarch-pkgver: "738.3317cc2"
---

# Installing graudit

> Grep rough source code auditing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graudit) (version `738.3317cc2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S graudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/wireghoul/graudit) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  #./configure --prefix "$pkgdir/usr/bin" --dbdir "$pkgdir/usr/share/graudit"
  #make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/graudit`

Backlinks: [[code-audit]] · [[graudit]] · [[BlackArch Tools]]

