---
tool: "sockstat"
build-system: "make"
upstream: "https://packages.debian.org/unstable/main/sockstat"
recipe: "blackarch"
nix-package: "sockstat"
aur-package: "sockstat"
source: "https://packages.debian.org/unstable/main/sockstat"
license: "unknown"
blackarch-pkgver: "0.4.1"
---

# Installing sockstat

> A tool to let you view information about open connections. It is similar to the tool of the same name that is included in FreeBSD, trying to faithfully reproduce as much functionality as is possible.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sockstat) (version `0.4.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S sockstat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://packages.debian.org/unstable/main/sockstat) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[sockstat]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sockstat    # ad-hoc run, no install
nix profile install nixpkgs#sockstat   # or persist it
```
