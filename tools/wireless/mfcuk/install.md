---
tool: "mfcuk"
build-system: "autotools"
upstream: "https://github.com/nfc-tools/mfcuk/"
recipe: "blackarch"
nix-package: "mfcuk"
source: "https://github.com/nfc-tools/mfcuk/"
license: "GPL2"
blackarch-pkgver: "0.3.8"
---

# Installing mfcuk

> MIFARE Classic Universal toolKit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mfcuk) (version `0.3.8`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mfcuk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://code.google.com/p/mfcuk/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libnfc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/mfcuk`

Backlinks: [[wireless]] · [[mfcuk]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mfcuk    # ad-hoc run, no install
nix profile install nixpkgs#mfcuk   # or persist it
```
