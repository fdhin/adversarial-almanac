---
tool: "ace"
build-system: "make"
upstream: "https://ucsniff.sourceforge.net/ace.html"
recipe: "blackarch"
nix-package: "ace"
aur-package: "ace"
source: "https://ucsniff.sourceforge.net/ace.html"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.11"
---

# Installing ace

> A simple yet powerful VoIP Corporate Directory enumeration tool that mimics the behavior of an IP Phone in order to download the name and extension entries that a given phone can display on its screen interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ace) (version `1.11`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ace    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://ucsniff.sourceforge.net/ace.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtirpc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[ace]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ace    # ad-hoc run, no install
nix profile install nixpkgs#ace   # or persist it
```
