---
tool: "outguess"
build-system: "autotools"
upstream: "https://github.com/resurrecting-open-source-projects/outguess"
recipe: "blackarch"
nix-package: "outguess"
aur-package: "outguess"
source: "https://github.com/resurrecting-open-source-projects/outguess"
license: "BSD"
blackarch-pkgver: "0.4"
---

# Installing outguess

> A universal steganographic tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/outguess) (version `0.4`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S outguess    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/resurrecting-open-source-projects/outguess) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./autogen.sh
  ./configure --prefix=/usr --mandir=/usr/share/man --with-generic-jconfig
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[outguess]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#outguess    # ad-hoc run, no install
nix profile install nixpkgs#outguess   # or persist it
```
