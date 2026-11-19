---
tool: "netdiscover"
build-system: "autotools"
upstream: "http://nixgeneration.com/~jaime/netdiscover/"
recipe: "blackarch"
nix-package: "netdiscover"
aur-package: "netdiscover"
source: "http://nixgeneration.com/~jaime/netdiscover/"
license: "GPL"
blackarch-pkgver: "257.42648f9"
kali-package: "netdiscover"
kali-version: "0.21-1"
---

# Installing netdiscover

> An active/passive address reconnaissance tool, mainly developed for those wireless networks without dhcp server, when you are wardriving. It can be also used on hub/switched networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netdiscover) (version `257.42648f9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S netdiscover    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install netdiscover    # Kali package version 0.21-1
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://nixgeneration.com/~jaime/netdiscover/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr --sysconfdir=/etc --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[netdiscover]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#netdiscover    # ad-hoc run, no install
nix profile install nixpkgs#netdiscover   # or persist it
```
