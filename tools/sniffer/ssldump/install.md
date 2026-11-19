---
tool: "ssldump"
build-system: "autotools"
upstream: "http://www.rtfm.com/ssldump/"
recipe: "blackarch"
nix-package: "ssldump"
aur-package: "ssldump"
source: "http://www.rtfm.com/ssldump/"
license: "custom:unknown"
blackarch-pkgver: "1.7"
kali-package: "ssldump"
kali-version: "1.9-2"
---

# Installing ssldump

> An SSLv3/TLS network protocol analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssldump) (version `1.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ssldump    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ssldump    # Kali package version 1.9-2
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.rtfm.com/ssldump/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `json-c`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./autogen.sh

  # Arch64 fixes.
  ./configure \
    --prefix=/usr \
    --build=i686-pc-linux-gnu \
    --host=i686-pc-linux-gnu \
    "--sbindir=$pkgdir/usr/bin"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[ssldump]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ssldump    # ad-hoc run, no install
nix profile install nixpkgs#ssldump   # or persist it
```
