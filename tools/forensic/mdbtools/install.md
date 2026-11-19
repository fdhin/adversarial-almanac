---
tool: "mdbtools"
build-system: "autotools"
upstream: "https://github.com/evanmiller/mdbtools"
recipe: "blackarch"
nix-package: "mdbtools"
aur-package: "mdbtools"
source: "https://github.com/evanmiller/mdbtools"
license: "GPL2"
blackarch-pkgver: "738.823b32f"
kali-package: "mdbtools"
kali-version: "1.0.1-0.1+b2"
---

# Installing mdbtools

> Utilities for viewing data and exporting schema from Microsoft Access Database files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mdbtools) (version `738.823b32f`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mdbtools    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install mdbtools    # Kali package version 1.0.1-0.1+b2
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/evanmiller/mdbtools) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bison`
  - `flex`
  - `txt2man`

## Exact build steps from the recipe

```bash
cd $pkgname

  autoreconf -i -f

  ./configure --prefix=/usr --sysconfdir=/etc --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[mdbtools]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mdbtools    # ad-hoc run, no install
nix profile install nixpkgs#mdbtools   # or persist it
```
