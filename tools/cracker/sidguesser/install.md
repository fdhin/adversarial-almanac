---
tool: "sidguesser"
build-system: "autotools"
upstream: "http://www.cqure.net/wp/tools/database/sidguesser/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/tools/database/sidguesser/"
license: "GPL3"
blackarch-pkgver: "1.0.5"
kali-package: "sidguesser"
kali-version: "1.0.5-1kali4"
---

# Installing sidguesser

> Guesses sids/instances against an Oracle database according to a predefined dictionary file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sidguesser) (version `1.0.5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sidguesser    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sidguesser    # Kali package version 1.0.5-1kali4
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/tools/database/sidguesser/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd SIDGuesser

  chmod +x ./configure

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[sidguesser]] · [[BlackArch Tools]]

