---
tool: "wolpertinger"
build-system: "autotools"
upstream: "https://github.com/Crapworks/wolpertinger"
recipe: "blackarch"
source: "https://github.com/Crapworks/wolpertinger.git"
license: "GPL3"
blackarch-pkgver: "3.683ad04"
provides: ["wolper-mcp", "wolper-mcp.py"]
---

# Installing wolpertinger

> A distributed portscanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wolpertinger) (version `3.683ad04`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wolpertinger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/Crapworks/wolpertinger.git
cd wolpertinger
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `linux-headers`
  - `python2`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sysconfdir=/etc
  else
    ./configure --build=arm --prefix=/usr --sysconfdir=/etc
  fi

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/wolper-mcp`, `/usr/bin/wolper-mcp.py`

Backlinks: [[scanner]] · [[wolpertinger]] · [[BlackArch Tools]]

