---
tool: "ntpdos"
build-system: "make"
upstream: "https://github.com/sepehrdaddev/ntpdos"
recipe: "blackarch"
source: "https://github.com/sepehrdaddev/ntpdos"
license: "GPL3"
blackarch-pkgver: "4.3fe389b"
---

# Installing ntpdos

> PoC for distributed NTP reflection DoS (CVE-5211)

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntpdos) (version `4.3fe389b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ntpdos    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/sepehrdaddev/ntpdos) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`, `make`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[ntpdos]] · [[BlackArch Tools]]

