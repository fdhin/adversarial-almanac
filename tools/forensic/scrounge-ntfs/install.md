---
tool: "scrounge-ntfs"
build-system: "autotools"
upstream: "http://thewalter.net/stef/software/scrounge/"
recipe: "blackarch"
nix-package: "scrounge-ntfs"
aur-package: "scrounge-ntfs"
source: "http://thewalter.net/stef/software/scrounge/"
license: "BSD"
blackarch-pkgver: "0.9"
kali-package: "scrounge-ntfs"
kali-version: "0.9-11"
---

# Installing scrounge-ntfs

> Data recovery program for NTFS file systems

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scrounge-ntfs) (version `0.9`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S scrounge-ntfs    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install scrounge-ntfs    # Kali package version 0.9-11
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://memberwebs.com/stef/software/scrounge/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[scrounge-ntfs]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#scrounge-ntfs    # ad-hoc run, no install
nix profile install nixpkgs#scrounge-ntfs   # or persist it
```
