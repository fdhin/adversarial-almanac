---
tool: "mac-robber"
build-system: "make"
upstream: "http://www.sleuthkit.org/mac-robber/download.php"
recipe: "blackarch"
nix-package: "mac-robber"
source: "http://www.sleuthkit.org/mac-robber/download.php"
license: "GPL2"
blackarch-pkgver: "1.02"
kali-package: "mac-robber"
kali-version: "1.02-14"
---

# Installing mac-robber

> A digital investigation tool that collects data from allocated files in a mounted file system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mac-robber) (version `1.02`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mac-robber    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install mac-robber    # Kali package version 1.02-14
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.sleuthkit.org/mac-robber/download.php) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[mac-robber]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mac-robber    # ad-hoc run, no install
nix profile install nixpkgs#mac-robber   # or persist it
```
