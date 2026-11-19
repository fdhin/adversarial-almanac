---
tool: "recoverjpeg"
build-system: "autotools"
upstream: "https://github.com/samueltardieu/recoverjpeg"
recipe: "blackarch"
nix-package: "recoverjpeg"
aur-package: "recoverjpeg"
source: "https://github.com/samueltardieu/recoverjpeg"
license: "GPL"
blackarch-pkgver: "2.6.3"
kali-package: "recoverjpeg"
kali-version: "2.6.3-7"
---

# Installing recoverjpeg

> Recover jpegs from damaged devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recoverjpeg) (version `2.6.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S recoverjpeg    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install recoverjpeg    # Kali package version 2.6.3-7
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/samueltardieu/recoverjpeg) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `glibc`
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[recoverjpeg]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#recoverjpeg    # ad-hoc run, no install
nix profile install nixpkgs#recoverjpeg   # or persist it
```
