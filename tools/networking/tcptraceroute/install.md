---
tool: "tcptraceroute"
build-system: "autotools"
upstream: "http://michael.toren.net/code/tcptraceroute/"
recipe: "blackarch"
nix-package: "tcptraceroute"
aur-package: "tcptraceroute"
source: "http://michael.toren.net/code/tcptraceroute/"
license: "GPL"
blackarch-pkgver: "1.5beta7"
---

# Installing tcptraceroute

> A traceroute implementation using TCP packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcptraceroute) (version `1.5beta7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tcptraceroute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://michael.toren.net/code/tcptraceroute/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sysconfdir=/etc
  else
    ./configure --build=arm --prefix=/usr --sysconfdir=/etc
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tcptraceroute]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#tcptraceroute    # ad-hoc run, no install
nix profile install nixpkgs#tcptraceroute   # or persist it
```
