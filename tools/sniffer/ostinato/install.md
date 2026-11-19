---
tool: "ostinato"
build-system: "make"
upstream: "https://github.com/pstavirs/ostinato/"
recipe: "blackarch"
nix-package: "ostinato"
aur-package: "ostinato"
source: "https://github.com/pstavirs/ostinato/"
license: "GPL3"
blackarch-pkgver: "1.2.0"
provides: ["drone"]
---

# Installing ostinato

> An open-source, cross-platform packet/traffic generator and analyzer with a friendly GUI. It aims to be "Wireshark in Reverse" and thus become complementary to Wireshark.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ostinato) (version `1.2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ostinato    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/pstavirs/ostinato/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `protobuf`
  - `protobuf-c`
  - `qt4`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  qmake-qt4 -Wnone PREFIX=/usr ost.pro

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/drone`

Backlinks: [[sniffer]] · [[ostinato]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ostinato    # ad-hoc run, no install
nix profile install nixpkgs#ostinato   # or persist it
```
