---
tool: "truecrack"
build-system: "autotools"
upstream: "http://code.google.com/p/truecrack/"
recipe: "blackarch"
nix-package: "truecrack"
source: "http://code.google.com/p/truecrack/"
license: "GPL3"
blackarch-pkgver: "35"
kali-package: "truecrack"
kali-version: "3.6+git20150326-0kali6"
---

# Installing truecrack

> Password cracking for truecrypt(c) volumes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/truecrack) (version `35`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S truecrack    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install truecrack    # Kali package version 3.6+git20150326-0kali6
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://code.google.com/p/truecrack/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `cuda`

## Exact build steps from the recipe

```bash
cd $pkgname

  export PATH=$PATH:/opt/cuda/bin

  ./configure --prefix=/usr --datarootdir=/usr/share --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[truecrack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#truecrack    # ad-hoc run, no install
nix profile install nixpkgs#truecrack   # or persist it
```
