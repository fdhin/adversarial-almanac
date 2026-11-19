---
tool: "pmacct"
build-system: "autotools"
upstream: "https://github.com/pmacct/pmacct"
recipe: "blackarch"
nix-package: "pmacct"
aur-package: "pmacct"
source: "https://github.com/pmacct/pmacct"
license: "GPL2"
blackarch-pkgver: "v1.8.0.r162.g50ec5ce"
---

# Installing pmacct

> Small set of multi-purpose passive network monitoring tools [NetFlow IPFIX sFlow libpcap BGP BMP IGP Streaming Telemetry].

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pmacct) (version `v1.8.0.r162.g50ec5ce`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pmacct    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/pmacct/pmacct) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `geoip`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `mariadb`
  - `rabbitmq`
  - `zeromq`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  autoreconf

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[pmacct]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pmacct    # ad-hoc run, no install
nix profile install nixpkgs#pmacct   # or persist it
```
