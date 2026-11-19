---
tool: "snort"
build-system: "autotools"
upstream: "http://www.snort.org/"
recipe: "blackarch"
nix-package: "snort"
aur-package: "snort"
source: "http://rules.emergingthreats.net/open/snort-2.9.0/emerging.rules.tar.gz"
license: "GPL"
blackarch-pkgver: "2.9.20"
---

# Installing snort

> A lightweight network intrusion detection system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snort) (version `2.9.20`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S snort    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
curl -fLO http://rules.emergingthreats.net/open/snort-2.9.0/emerging.rules.tar.gz
tar xf emerging.rules.tar.gz
cd emerging.rules
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ca-certificates`
  - `libdaq`
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtirpc`
  - `libutil-linux`
  - `luajit`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pcre`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$_pkgver"

  CPPFLAGS="$CPPFLAGS -I/usr/include/tirpc/" ./configure --prefix=/usr \
    --sysconfdir="/etc/$pkgname" --with-libpcap-includes=/usr/include/pcap \
    --with-daq-includes=/usr/include --with-daq-libraries=/usr/lib/daq \
    --disable-static-daq --enable-zlib --enable-sourcefire --enable-large-pcap \
    --enable-64bit-gcc --enable-linux-smp-stats --enable-large-pcap

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[snort]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snort    # ad-hoc run, no install
nix profile install nixpkgs#snort   # or persist it
```
