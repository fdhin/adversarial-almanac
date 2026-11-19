---
tool: "packet-o-matic"
build-system: "autotools"
upstream: "http://www.packet-o-matic.org/"
recipe: "blackarch"
source: "http://www.packet-o-matic.org/"
license: "GPL"
blackarch-pkgver: "1413.78fcd48"
---

# Installing packet-o-matic

> A real time packet processor. Reads the packet from an input module, match the packet using rules and connection tracking information and then send it to a target module.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/packet-o-matic) (version `1413.78fcd48`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S packet-o-matic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/gmsoft-tuxicoman/pom-ng.git
cd pom-ng
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libexif`
  - `libmicrohttpd`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtool`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `lua`
  - `pkg-config`
  - `uthash`
  - `xmlrpc-c`

## Exact build steps from the recipe

```bash
cd $pkgname

  export lua_CFLAGS="-llua"
  export lua_LIBS="-llua"

  autoreconf -f -i
  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[packet-o-matic]] · [[BlackArch Tools]]

