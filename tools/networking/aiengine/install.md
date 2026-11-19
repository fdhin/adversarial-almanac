---
tool: "aiengine"
build-system: "autotools"
upstream: "https://bitbucket.org/camp0/aiengine/downloads/"
recipe: "blackarch"
source: "https://bitbucket.org/camp0/aiengine/downloads/"
license: "GPL3"
blackarch-pkgver: "2.4.0"
---

# Installing aiengine

> A packet inspection engine with capabilities of learning without any human intervention.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aiengine) (version `2.4.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S aiengine    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://bitbucket.org/camp0/aiengine/downloads/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `boost-libs`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./autogen.sh

  export PYTHON_VERSION=3.10

  ./configure --prefix=/usr --enable-tcpqos --enable-reject --enable-pythongil

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[aiengine]] · [[BlackArch Tools]]

