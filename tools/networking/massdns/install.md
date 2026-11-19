---
tool: "massdns"
build-system: "make"
upstream: "https://github.com/blechschmidt/massdns"
recipe: "blackarch"
nix-package: "massdns"
aur-package: "massdns"
source: "https://github.com/blechschmidt/massdns"
license: "GPL3"
blackarch-pkgver: "318.30c3809"
---

# Installing massdns

> A high-performance DNS stub resolver in C.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/massdns) (version `318.30c3809`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S massdns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/blechschmidt/massdns) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ldns`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make PREFiX="$pkgdir/usr"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[massdns]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#massdns    # ad-hoc run, no install
nix profile install nixpkgs#massdns   # or persist it
```
