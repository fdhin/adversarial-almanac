---
tool: "cymothoa"
build-system: "make"
upstream: "http://cymothoa.sourceforge.net/"
recipe: "blackarch"
source: "https://github.com/BlackArch/cymothoa-bin"
license: "GPL2"
blackarch-pkgver: "1"
provides: ["cymothoa-udp_server"]
kali-package: "cymothoa"
kali-version: "1-beta-1kali4"
---

# Installing cymothoa

> A stealth backdooring tool, that inject backdoor's shellcode into an existing process.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cymothoa) (version `1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S cymothoa    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install cymothoa    # Kali package version 1-beta-1kali4
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/BlackArch/cymothoa-bin
cd cymothoa-bin
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver-beta"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/cymothoa-udp_server`

Backlinks: [[backdoor]] · [[cymothoa]] · [[BlackArch Tools]]

