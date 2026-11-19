---
tool: "johnny"
build-system: "make"
upstream: "http://openwall.info/wiki/john/johnny"
recipe: "blackarch"
nix-package: "johnny"
source: "http://openwall.info/wiki/john/johnny"
license: "GPL"
blackarch-pkgver: "20120424"
kali-package: "johnny"
kali-version: "2.2+git20160807-0kali2+b1"
---

# Installing johnny

> GUI for John the Ripper.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/johnny) (version `20120424`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S johnny    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install johnny    # Kali package version 2.2+git20160807-0kali2+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://openwall.info/wiki/john/johnny) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `john`
  - `qt4`

## Exact build steps from the recipe

```bash
cd $pkgname

  /usr/lib/qt4/bin/qmake

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/pixmaps`

Backlinks: [[cracker]] · [[johnny]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#johnny    # ad-hoc run, no install
nix profile install nixpkgs#johnny   # or persist it
```
