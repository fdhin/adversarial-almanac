---
tool: "firewalk"
build-system: "autotools"
upstream: "http://packetfactory.openwall.net/projects/firewalk/"
recipe: "blackarch"
nix-package: "firewalk"
aur-package: "firewalk"
source: "http://packetfactory.openwall.net/projects/firewalk/"
license: "BSD-2-Clause"
blackarch-pkgver: "5.0"
kali-package: "firewalk"
kali-version: "5.0-6+b1"
---

# Installing firewalk

> An active reconnaissance network security tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firewalk) (version `5.0`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S firewalk    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install firewalk    # Kali package version 5.0-6+b1
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://packetfactory.openwall.net/projects/firewalk/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd Firewalk

  if [[ $CARCH == "aarch64" ]]; then
    ./configure --prefix=/usr --build=aarch64-unknown-linux-gnu
  else
    ./configure --prefix=/usr --build=x86_64-pc-linux-gnu
  fi
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[firewalk]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#firewalk    # ad-hoc run, no install
nix profile install nixpkgs#firewalk   # or persist it
```
