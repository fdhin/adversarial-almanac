---
tool: "argus-clients"
build-system: "autotools"
upstream: "http://qosient.com/argus/"
recipe: "blackarch"
nix-package: "argus-clients"
source: "http://qosient.com/argus/"
license: "GPL3"
blackarch-pkgver: "3.0.8.3"
---

# Installing argus-clients

> Network monitoring client for Argus.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/argus-clients) (version `3.0.8.3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S argus-clients    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://qosient.com/argus/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `geoip`
  - `libedit`
  - `libmariadbclient` — Debian libmariadb-dev / Fedora mariadb-devel
  - `libsasl`
  - `libtirpc-compat`
  - `pcre`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --sbindir=/usr/bin --prefix=/usr --with-sasl --with-GeoIP \
    --with-mysql --with-libpcre --with-libft --with-readline --with-perl

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[argus-clients]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#argus-clients    # ad-hoc run, no install
nix profile install nixpkgs#argus-clients   # or persist it
```
