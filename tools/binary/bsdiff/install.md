---
tool: "bsdiff"
build-system: "make"
upstream: "https://www.daemonology.net/bsdiff/"
recipe: "blackarch"
nix-package: "bsdiff"
aur-package: "bsdiff"
source: "https://security.freebsd.org/patches/SA-16:25/bspatch.patch"
license: "BSD"
blackarch-pkgver: "4.3"
---

# Installing bsdiff

> Tools for building and applying patches to binary files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bsdiff) (version `4.3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S bsdiff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/freebsd/freebsd/commit/d0260bc283.patch
cd d0260bc283.patch
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bzip2`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[bsdiff]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bsdiff    # ad-hoc run, no install
nix profile install nixpkgs#bsdiff   # or persist it
```
