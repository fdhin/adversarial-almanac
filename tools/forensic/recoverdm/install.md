---
tool: "recoverdm"
build-system: "make"
upstream: "http://www.vanheusden.com/recoverdm/"
recipe: "blackarch"
nix-package: "recoverdm"
aur-package: "recoverdm"
source: "http://www.vanheusden.com/recoverdm/"
license: "GPL"
blackarch-pkgver: "0.20"
kali-package: "recoverdm"
kali-version: "0.20-9"
---

# Installing recoverdm

> Recover damaged CD DVD and disks with bad sectors.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recoverdm) (version `0.20`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S recoverdm    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install recoverdm    # Kali package version 0.20-9
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.vanheusden.com/recoverdm/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[recoverdm]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#recoverdm    # ad-hoc run, no install
nix profile install nixpkgs#recoverdm   # or persist it
```
