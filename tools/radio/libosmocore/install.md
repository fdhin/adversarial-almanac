---
tool: "libosmocore"
build-system: "make"
upstream: "https://osmocom.org/projects/libosmocore/wiki/Libosmocore"
recipe: "blackarch"
nix-package: "libosmocore"
aur-package: "libosmocore"
source: "https://osmocom.org/projects/libosmocore/wiki/Libosmocore"
license: "GPL"
blackarch-pkgver: "1.14.0"
---

# Installing libosmocore

> Collection of common code used in various sub-projects inside the Osmocom family of projects.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libosmocore) (version `1.14.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S libosmocore    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://osmocom.org/projects/libosmocore/wiki/Libosmocore) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libmnl`
  - `liburing`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `lksctp-tools`
  - `pcsclite`
  - `python`
  - `talloc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[libosmocore]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#libosmocore    # ad-hoc run, no install
nix profile install nixpkgs#libosmocore   # or persist it
```
