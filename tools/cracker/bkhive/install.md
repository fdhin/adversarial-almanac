---
tool: "bkhive"
build-system: "make"
upstream: "http://sourceforge.net/projects/ophcrack"
recipe: "blackarch"
source: "http://sourceforge.net/projects/ophcrack"
license: "GPL"
blackarch-pkgver: "1.1.1"
---

# Installing bkhive

> Program for dumping the syskey bootkey from a Windows NT/2K/XP system hive.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bkhive) (version `1.1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bkhive    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/ophcrack) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[cracker]] · [[bkhive]] · [[BlackArch Tools]]

