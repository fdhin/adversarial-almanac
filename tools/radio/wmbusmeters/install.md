---
tool: "wmbusmeters"
build-system: "make"
upstream: "https://github.com/wmbusmeters/wmbusmeters"
recipe: "blackarch"
nix-package: "wmbusmeters"
aur-package: "wmbusmeters"
source: "https://github.com/wmbusmeters/wmbusmeters"
license: "unknown"
blackarch-pkgver: "1.17.1"
---

# Installing wmbusmeters

> Read the wired or wireless mbus protocol to acquire utility meter readings.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wmbusmeters) (version `1.17.1`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S wmbusmeters    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/wmbusmeters/wmbusmeters) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`
  - `glibc`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make STRIP=true COMMIT_HASH=$pkgrel TAG=$pkgver BRANCH="master" \
    CHANGES="nochanges"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[wmbusmeters]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wmbusmeters    # ad-hoc run, no install
nix profile install nixpkgs#wmbusmeters   # or persist it
```
