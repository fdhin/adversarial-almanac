---
tool: "lanmap2"
build-system: "make"
upstream: "http://github.com/rflynn/lanmap2"
recipe: "blackarch"
source: "http://github.com/rflynn/lanmap2"
license: "GPL"
blackarch-pkgver: "127.1197999"
provides: ["lanmap2-cap", "lanmap2-graph"]
---

# Installing lanmap2

> Passive network mapping tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lanmap2) (version `127.1197999`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S lanmap2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://github.com/rflynn/lanmap2) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`
  - `graphviz`
  - `libbsd`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `php`
  - `sqlite3`

## Exact build steps from the recipe

```bash
cd $pkgname

  make all
```

## Installed layout (per recipe)

- Commands: `/usr/bin/lanmap2-cap`, `/usr/bin/lanmap2-graph`

Backlinks: [[recon]] · [[lanmap2]] · [[BlackArch Tools]]

