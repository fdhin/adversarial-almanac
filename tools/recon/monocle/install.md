---
tool: "monocle"
build-system: "autotools"
upstream: "http://packetstormsecurity.com/files/99823/Monocle-Host-Discovery-Tool-1.0.html"
recipe: "blackarch"
nix-package: "monocle"
aur-package: "monocle"
source: "http://packetstormsecurity.com/files/99823/Monocle-Host-Discovery-Tool-1.0.html"
license: "GPL3"
blackarch-pkgver: "1.0"
---

# Installing monocle

> A local network host discovery tool. In passive mode, it will listen for ARP request and reply packets. In active mode, it will send ARP requests to the specific IP range. The results are a list of IP and MAC addresses present on the local network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/monocle) (version `1.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S monocle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/99823/Monocle-Host-Discovery-Tool.0.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  # Makefile needs to be fixed. we go with this:
  cd src
  gcc error.c main.c mem.c netconf.c socket.c -O0 -W -o ../$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[monocle]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#monocle    # ad-hoc run, no install
nix profile install nixpkgs#monocle   # or persist it
```
