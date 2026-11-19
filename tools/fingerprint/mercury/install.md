---
tool: "mercury"
build-system: "make"
upstream: "https://github.com/cisco/mercury"
recipe: "blackarch"
nix-package: "mercury"
aur-package: "mercury"
source: "https://github.com/cisco/mercury"
license: "custom:unknown"
blackarch-pkgver: "2.21.0.r0.gd64fa8f"
---

# Installing mercury

> Network metadata capture and analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mercury) (version `2.21.0.r0.gd64fa8f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mercury    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/cisco/mercury) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`
- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `gmp`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `tcpreplay`
  - `valgrind`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make -j1
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`

Backlinks: [[fingerprint]] · [[mercury]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mercury    # ad-hoc run, no install
nix profile install nixpkgs#mercury   # or persist it
```
