---
tool: "dmg2img"
build-system: "make"
upstream: "http://vu1tur.eu.org/tools/"
recipe: "blackarch"
nix-package: "dmg2img"
aur-package: "dmg2img"
source: "http://vu1tur.eu.org/tools/"
license: "GPL2"
blackarch-pkgver: "1.6.7"
---

# Installing dmg2img

> A CLI tool to uncompress Apple's compressed DMG files to the HFS+ IMG format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dmg2img) (version `1.6.7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dmg2img    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://vu1tur.eu.org/tools/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bzip2`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

	make PREFIX=/usr
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dmg2img]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dmg2img    # ad-hoc run, no install
nix profile install nixpkgs#dmg2img   # or persist it
```
