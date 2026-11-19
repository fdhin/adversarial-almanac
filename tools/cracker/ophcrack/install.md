---
tool: "ophcrack"
build-system: "autotools"
upstream: "http://ophcrack.sourceforge.net"
recipe: "blackarch"
nix-package: "ophcrack"
aur-package: "ophcrack"
source: "http://ophcrack.sourceforge.net"
license: "GPL"
blackarch-pkgver: "3.8.0"
kali-package: "ophcrack"
kali-version: "3.8.0-4"
---

# Installing ophcrack

> Windows password cracker based on rainbow tables.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ophcrack) (version `3.8.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ophcrack    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ophcrack    # Kali package version 3.8.0-4
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://ophcrack.sourceforge.net) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `qt5-charts`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --enable-gui --enable-graph

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[cracker]] · [[ophcrack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ophcrack    # ad-hoc run, no install
nix profile install nixpkgs#ophcrack   # or persist it
```
