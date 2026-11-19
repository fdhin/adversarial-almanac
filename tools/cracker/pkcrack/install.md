---
tool: "pkcrack"
build-system: "make"
upstream: "https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack/download1.html"
recipe: "blackarch"
nix-package: "pkcrack"
aur-package: "pkcrack"
source: "https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack/download1.html"
license: "custom:unknown"
blackarch-pkgver: "1.2.3"
---

# Installing pkcrack

> A PkZip encryption cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pkcrack) (version `1.2.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pkcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack/download1.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver/src"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pkcrack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pkcrack    # ad-hoc run, no install
nix profile install nixpkgs#pkcrack   # or persist it
```
