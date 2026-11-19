---
tool: "unicornscan"
build-system: "autotools"
upstream: "https://github.com/robertelee78/unicornscan"
recipe: "blackarch"
aur-package: "unicornscan"
source: "https://github.com/robertelee78/unicornscan"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.4.43"
provides: ["unicornscan-alicorn", "unicornscan-geoip-update"]
kali-package: "unicornscan"
kali-version: "0.4.7-1kali7"
---

# Installing unicornscan

> Asynchronous, stateless TCP/UDP scanner for scalable, high-speed network reconnaissance. Includes Alicorn web UI for result visualization.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unicornscan) (version `0.4.43`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S unicornscan    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install unicornscan    # Kali package version 0.4.7-1kali7
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/robertelee78/unicornscan) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libcap`
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libltdl`
  - `libmaxminddb`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `postgresql`
  - `postgresql-libs` — Debian libpq-dev / Fedora libpq-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  # Configure with PostgreSQL support
  # -Wno-error required for legacy C code with modern GCC
  if [[ "$CARCH" == "aarch64" ]]; then
    ./configure CFLAGS="$CFLAGS -D_GNU_SOURCE -Wno-error" \
      --prefix=/usr \
      --sysconfdir=/etc \
      --localstatedir=/var/lib \
      --with-pgsql \
      --build=aarch64-unknown-linux-gnu
  else
    ./configure CFLAGS="$CFLAGS -D_GNU_SOURCE -Wno-error" \
      --prefix=/usr \
      --sysconfdir=/etc \
      --localstatedir=/var/lib \
      --with-pgsql
  fi

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/unicornscan-alicorn`, `/usr/bin/unicornscan-geoip-update`
- Data: `/usr/share/unicornscan`

Backlinks: [[scanner]] · [[unicornscan]] · [[BlackArch Tools]]

