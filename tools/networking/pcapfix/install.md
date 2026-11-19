---
tool: "pcapfix"
build-system: "make"
upstream: "http://f00l.de/pcapfix/"
recipe: "blackarch"
nix-package: "pcapfix"
aur-package: "pcapfix"
source: "http://f00l.de/pcapfix/"
license: "GPL3"
blackarch-pkgver: "1.1.7"
---

# Installing pcapfix

> Tries to repair your broken pcap and pcapng files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcapfix) (version `1.1.7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pcapfix    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://f00l.de/pcapfix/) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[pcapfix]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pcapfix    # ad-hoc run, no install
nix profile install nixpkgs#pcapfix   # or persist it
```
