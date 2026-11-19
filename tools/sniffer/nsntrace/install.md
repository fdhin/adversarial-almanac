---
tool: "nsntrace"
build-system: "autotools"
upstream: "https://github.com/jonasdn/nsntrace"
recipe: "blackarch"
source: "https://github.com/jonasdn/nsntrace.git"
license: "GPL2"
blackarch-pkgver: "86.1d5adce"
---

# Installing nsntrace

> Perform network trace of a single process by using network namespaces.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nsntrace) (version `86.1d5adce`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S nsntrace    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/jonasdn/nsntrace.git
cd nsntrace
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `automake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `dbus`
  - `docbook-xsl`
  - `glibc`
  - `iptables`
  - `libgcrypt`
  - `libgpg-error`
  - `libnl`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libsystemd`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `lz4`
  - `xz`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[nsntrace]] · [[BlackArch Tools]]

