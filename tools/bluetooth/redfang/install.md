---
tool: "redfang"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/31864/redfang.2.5.tar.gz.html"
recipe: "blackarch"
nix-package: "redfang"
source: "http://packetstormsecurity.com/files/31864/redfang.2.5.tar.gz.html"
license: "custom"
blackarch-pkgver: "2.5"
kali-package: "redfang"
kali-version: "2.5-1kali5"
---

# Installing redfang

> Finds non-discoverable Bluetooth devices by brute-forcing the last six bytes of the devices' Bluetooth addresses and calling read_remote_name().

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redfang) (version `2.5`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S redfang    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install redfang    # Kali package version 2.5-1kali5
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/31864/redfang.2.5.tar.gz.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[redfang]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#redfang    # ad-hoc run, no install
nix profile install nixpkgs#redfang   # or persist it
```
