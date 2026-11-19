---
tool: "scalpel"
build-system: "autotools"
upstream: "http://www.digitalforensicssolutions.com/Scalpel/"
recipe: "blackarch"
nix-package: "scalpel"
source: "http://www.digitalforensicssolutions.com/Scalpel/"
license: "GPL2"
blackarch-pkgver: "1.1687261"
kali-package: "scalpel"
kali-version: "1.60+git20240110.6960eb2-2"
---

# Installing scalpel

> A frugal, high performance file carver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scalpel) (version `1.1687261`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S scalpel    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install scalpel    # Kali package version 1.60+git20240110.6960eb2-2
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone http://github.com/BlackArch/Scalpel-2.0.git
cd Scalpel-2.0
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `tre`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[scalpel]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#scalpel    # ad-hoc run, no install
nix profile install nixpkgs#scalpel   # or persist it
```
