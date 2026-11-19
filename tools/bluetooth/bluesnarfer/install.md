---
tool: "bluesnarfer"
build-system: "make"
upstream: "http://www.alighieri.org/project.html"
recipe: "blackarch"
nix-package: "bluesnarfer"
source: "http://www.alighieri.org/project.html"
license: "custom"
blackarch-pkgver: "0.1"
kali-package: "bluesnarfer"
kali-version: "0.1-1kali4"
---

# Installing bluesnarfer

> A bluetooth attacking tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluesnarfer) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S bluesnarfer    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install bluesnarfer    # Kali package version 0.1-1kali4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.alighieri.org/project.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluesnarfer]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bluesnarfer    # ad-hoc run, no install
nix profile install nixpkgs#bluesnarfer   # or persist it
```
